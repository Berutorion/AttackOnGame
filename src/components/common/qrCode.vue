<template>
    <div>
        <qrcode-stream
            :camera="camera"
            :track="paintBoundingBox"
            :barcode-formats="selectedBarcodeFormats"
            @decode="onDecode"
            @detect="onDetect"
            @init="onInit"
            @camera-on="onCameraReady"
        >
        </qrcode-stream>
        <div v-if="decodedString" class="decoded-result">
            解碼結果~: {{ decodedString }}
        </div>
    </div>
</template>

<script setup>
import { QrcodeStream } from 'vue-qrcode-reader';
import { ref, onMounted } from 'vue';

const camera = ref('user');
const selectedBarcodeFormats = ref(['qr_code']);
const decodedString = ref('');

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

function onDecode(decodedValue) {
    console.log('Decoded string:', decodedValue);
    decodedString.value = decodedValue;
}
function onDetect(detection) {
    console.log('QR code detected but not decoded yet:', detection);
    console.log('rawValue:', detection[0].rawValue);
}
async function onInit(promise) {
    console.log('onInit called...');
    promise
        .then(() => {
            console.log('Initialization successful');
        })
        .catch((error) => {
            console.error('Initialization error:', error);
            if (error.name === 'NotAllowedError') {
                alert('請允許訪問相機');
            }
        });
}

function onCameraReady() {
    console.log('Camera is ready');
}
onMounted(() => {
    camera.value = 'user';
});
</script>
