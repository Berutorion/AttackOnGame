<template>
    <div>
        <qrcode-stream
            :camera="camera"
            :track="paintBoundingBox"
            :barcode-formats="selectedBarcodeFormats"
            @detect="onDetect"
            @camera-on="onCameraReady"
            @camera-off="onCameraOff"
        >
            <div v-show="showScanConfirmation" class="scan-confirmation"></div>
        </qrcode-stream>

        <div v-if="usedQrcodes" class="decoded-result">
            <div
                v-for="used in usedQrcodes"
                :key="used"
                class="d-flex justify-content-center text-primary"
            >
                <span class="material-symbols-outlined px-1">
                    check_circle
                </span>
                <p>{{ used }}</p>
            </div>
        </div>
        <div v-if="errorQrcodes" class="decoded-result">
            <div
                v-for="error in errorQrcodes"
                :key="error"
                class="d-flex justify-content-center text-grey33"
            >
                <span class="material-symbols-outlined px-1"> error </span>
                <p>{{ error }}</p>
            </div>
        </div>
        <div v-if="loading" class="text-center mt-4">
            <div class="spinner-border" role="status"></div>
        </div>
    </div>
</template>

<script setup>
import { QrcodeStream } from 'vue-qrcode-reader';
import { ref, onMounted, defineProps, defineEmits } from 'vue';
import StoreAPI from '@/api/Store';

const loading = ref(false);
const emit = defineEmits(['getTickets']);
const camera = ref('user');
const selectedBarcodeFormats = ref(['qr_code']);
const showScanConfirmation = ref(false);
const usedQrcodes = ref([]);
const errorQrcodes = ref([]);
const props = defineProps({
    codes: {
        type: Array,
        default: () => [],
    },
    idNumber: {
        type: String,
        default: '',
    },
});
console.log(props.codes);
function paintBoundingBox(detectedCodes, ctx) {
    detectedCodes.forEach((detectedCode) => {
        const {
            boundingBox: { x, y, width, height },
        } = detectedCode;

        ctx.lineWidth = 5;
        ctx.strokeStyle = '#0088CC';
        ctx.strokeRect(x, y, width, height);
    });
}
function validateTicketFormat(ticket) {
    const regex = /^ticket-\d{6}-[a-z0-9]{4}$/;
    return regex.test(ticket);
}
function validateTicketAccess(ticket) {
    console.log(props.codes);
    return props.codes.includes(ticket);
}

async function Scanning() {
    loading.value = true;
    await StoreAPI.validateQrCode(props.idNumber, {
        tickets: usedQrcodes.value,
    })
        .then(() => {
            emit('getTickets');
        })
        .finally(() => {
            loading.value = false;
        });
}
async function onDetect(detection) {
    usedQrcodes.value = [];
    detection.forEach((e) => {
        console.log(e.rawValue);
        if (
            validateTicketFormat(e.rawValue) &&
            validateTicketAccess(e.rawValue)
        ) {
            if (!usedQrcodes.value.includes(e.rawValue)) {
                console.log('sdd', e.rawValue);
                usedQrcodes.value.push(e.rawValue);
            }
        } else if (!errorQrcodes.value.includes(e.rawValue)) {
            errorQrcodes.value.push(e.rawValue);
        }
    });
    Scanning();
}
function onCameraReady() {
    showScanConfirmation.value = false;
}
function onCameraOff() {
    showScanConfirmation.value = true;
}
onMounted(() => {
    camera.value = 'user';
});
</script>
<style scoped>
.scan-confirmation {
    position: absolute;
    width: 100%;
    height: 100%;

    background-color: rgba(255, 255, 255, 0.8);

    display: flex;
    flex-flow: row nowrap;
    justify-content: center;
}
</style>
