<template>
    <div class="container-xxl ticket-scanner-wrap">
        <div class="row p-3">
            <Loading
                v-if="isLoading"
                :class="{ 'loading-fade': !isLoading }"
            ></Loading>
            <LeftEl></LeftEl>
            <div class="col-9 ps-3">
                <div class="border rounded bg-white min-h-screen p-3">
                    <div>
                        <div class="d-flex flex-row-reverse">
                            <rollBack
                                :route-link="{
                                    name: 'StoreActivity',
                                    params: idNumber || '',
                                }"
                            ></rollBack>
                        </div>
                        <div class="camera-container p-3">
                            <QrCode
                                class="camera-view"
                                :codes="codeList"
                                :id-number="idNumber"
                                @getTickets="getTickets()"
                            ></QrCode>
                        </div>
                    </div>
                    <div
                        class="d-grid pb-2 pt-3 gap-2 text-grey9F border-bottom"
                        style="grid-template-columns: 2fr 1fr 2fr 2fr"
                    >
                        <p>報名者</p>
                        <p>報到狀態</p>
                        <p>票券編號</p>
                        <p>報到時間</p>
                    </div>
                    <div v-if="tickets.length === 0">
                        <EmptyField text="還沒有人下訂單唷"></EmptyField>
                    </div>
                    <div v-else>
                        <div
                            v-for="user in ticketsAttr"
                            :key="user.idNumber"
                            class="d-grid gap-2 py-2 border-bottom"
                            style="grid-template-columns: 2fr 1fr 2fr 2fr"
                        >
                            <div class="d-flex align-items-center">
                                <div
                                    class="profile-img rounded-circle small-profile-img mx-1"
                                >
                                    <img :src="user.avatar" :alt="user.name" />
                                </div>
                                <p class="line-clamp-1 line-clamp">
                                    {{ user.name }}
                                </p>
                            </div>
                            <p>{{ user.status }}</p>
                            <p>{{ user.idNumber }}</p>
                            <p>{{ user.qrCodeUsedTime }}</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script setup>
import LeftEl from '@/components/store/StoreLeftEl.vue';
import StoreAPI from '@/api/Store';
import { computed, onMounted, ref } from 'vue';
import { useRoute, useRouter } from 'vue-router';
// import toLocalString from '@/utilities/toLocalString';
import TICKET_STATUS_MAP from '@/constant/ticketStatus';
import EmptyField from '@/components/common/EmptyField.vue';
import Loading from '@/components/common/Loading.vue';
import rollBack from '@/components/common/rollBack.vue';
import QrCode from '@/components/common/qrCode.vue';

const idNumber = ref('');
const tickets = ref([
    {
        name: '',
        avatar: '',
        idNumber: '',
        status: '',
        isActive: '',
    },
]);
const router = useRouter();
const route = useRoute();
const isLoading = ref(true);

const getTickets = async () => {
    await StoreAPI.getTheTickets(idNumber.value)
        .then((res) => {
            tickets.value = res.data.data;
        })
        .catch((err) => {
            if (err.response.status === 401) {
                alert('請先完成登入');
                router.push({
                    name: 'PlayerLogin',
                });
            } else {
                alert(`${err.response.data.message}`);
            }
        })
        .finally(() => {
            setTimeout(() => {
                isLoading.value = false;
            }, 500);
        });
};
onMounted(() => {
    console.log(route.params);
    idNumber.value = route.params.idNumber || '';
    getTickets();
});
function addSerialNumberToDuplicates(data) {
    const nameCount = {};
    const updatedData = data.map((item) => {
        if (!nameCount[item.name]) {
            nameCount[item.name] = 1;
        } else {
            nameCount[item.name] += 1;
        }
        return {
            ...item,
            name:
                nameCount[item.name] > 1
                    ? `${item.name} (${nameCount[item.name]})`
                    : item.name,
        };
    });
    return updatedData;
}
const ticketsAttr = computed(() => {
    const nameList = addSerialNumberToDuplicates(tickets.value);
    return nameList.map((x) => ({
        name: x.name,
        avatar: x.avatar,
        idNumber: x.idNumber,
        status: TICKET_STATUS_MAP[x.qrCodeStatus],
        isActive: '',
        qrCodeUsedTime: x.qrCodeUsedTime,
    }));
});
const codeList = computed(() => {
    return tickets.value.map((x) => x.idNumber);
});
</script>
<style lang="scss" scoped>
body {
    background-color: #f7f7f7;
}
.ticket-scanner-wrap {
    .camera-container {
        width: 100%;
        max-width: 600px;
        margin: 0 auto;
        position: relative;
    }

    .camera-view {
        width: 100%;
        height: auto;
    }
}
.profile-img {
    width: 54px;
    height: 54px;
    overflow: hidden;
    background-color: #fff;
    cursor: pointer;
    &.small-profile-img {
        width: 2rem;
        height: 2rem;
    }
    img {
        width: 100%;
        height: 100%;
        object-fit: cover;
    }
}
</style>
