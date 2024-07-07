<template>
    <div class="store-introduction container-fluid">
        <Loading
            v-if="isLoading"
            :class="{ 'loading-fade': !isLoading }"
        ></Loading>
        <div class="container pt-2">
            <div class="row">
                <div class="col-12">
                    <div
                        class="store-introduction__banner rounded-4 mb-4 bg-white border border-2 border-greyE9"
                    >
                        <div
                            class="banner__background rounded-top-4"
                            :style="{ height: '300px' }"
                        >
                            <img
                                referrerpolicy="no-referrer"
                                class="w-100 inset-0 object-fit-cover rounded-top-4"
                                height="300px"
                                :src="storeViewObject.avatar"
                            />
                        </div>
                        <div class="banner__content row p-4">
                            <div class="col-3 col-lg-2">
                                <img
                                    referrerpolicy="no-referrer"
                                    width="160px"
                                    height="160px"
                                    class="rounded-circle object-fit-cover mb-2 border"
                                    :src="storeViewObject.avatar"
                                    :alt="storeViewObject.name"
                                />
                            </div>

                            <div class="col-9 col-lg-10">
                                <div
                                    class="row justify-content-between align-items-top"
                                >
                                    <div class="col-6 col-lg-8">
                                        <h6
                                            class="pb-3"
                                            :style="{
                                                borderBottom:
                                                    '1px dashed #c9c9c9',
                                            }"
                                        >
                                            {{ storeViewObject.name }}
                                        </h6>
                                        <p class="mb-2">
                                            {{ storeViewObject.address }}
                                        </p>
                                        <p class="mb-2">
                                            {{ storeViewObject.introduce }}
                                        </p>
                                    </div>
                                    <div class="col-3 col-lg-2">
                                        <button class="btn btn-primary">
                                            與我們聊聊
                                        </button>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="sub-title_wrap mb-3">
                        <h2
                            class="fs-6 text-primary fw-bold pb-2 border-bottom border-2 border-primary fz-6 d-inline-block sub-title mb-0"
                        >
                            店家評價
                        </h2>
                    </div>
                    <div
                        class="bg-warning bg-opacity-10 p-3 border-2 border border-warning"
                    >
                        <div class="text-center">
                            <p class="fw-bold">
                                <span class="fs-5">4.5</span>/5
                            </p>
                            <div
                                class="position-relative"
                                style="
                                    width: 100px;
                                    height: 20px;
                                    margin: 0 auto;
                                    position: relative;
                                "
                            >
                                <div
                                    class="position-absolute"
                                    :style="{
                                        backgroundImage: 'url(' + starImg + ')',
                                    }"
                                    alt=""
                                    style="
                                        position: absolute;
                                        border: 0px solid red;
                                        margin: 0;
                                        padding: 0;
                                        min-height: 20px;
                                        height: 100%;
                                        width: 100%;
                                        display: block;
                                        background-repeat: no-repeat;
                                    "
                                ></div>
                                <div
                                    class="position-relative"
                                    :style="{
                                        backgroundImage:
                                            'url(' + starImgFill + ')',
                                    }"
                                    alt=""
                                    style="
                                        border: 0px solid blue;
                                        margin: 0;
                                        padding: 0;
                                        min-height: 20px;
                                        height: 100%;
                                        display: block;
                                        overflow: hidden;
                                        background-repeat: no-repeat;
                                    "
                                ></div>
                            </div>
                        </div>
                        <div></div>
                    </div>

                    <div class="store-introduction__content">
                        <div class="sub-title_wrap mb-3">
                            <h2
                                class="fs-6 text-primary fw-bold pb-2 border-bottom border-2 border-primary fz-6 d-inline-block sub-title mb-0"
                            >
                                其他活動
                            </h2>
                        </div>
                        <EventPanel
                            :data="eventCards"
                            :keywords="keywords"
                        ></EventPanel>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import { useRoute } from 'vue-router';
import EventAPI from '@/api/Event';
import StoreAPI from '@/api/Store';
import { selectStoreData } from '@/stores/selectStore';
import EventPanel from '@/components/event/EventPanel.vue';
import Loading from '@/components/common/Loading.vue';
import starImg from '@/assets/images/star.svg';
import starImgFill from '@/assets/images/star_fill.svg';

const isLoading = ref(true);
const route = useRoute();

const userId = ref(null);
const keywords = ref('');

const storeViewObject = ref({
    id: '',
    name: '',
    user: '',
    avatar: '',
    introduce: '',
    address: '',
    phone: '',
});

const eventCards = ref([]);
const selectTheStore = selectStoreData();
onMounted(() => {
    userId.value = route.params.userId;

    StoreAPI.get(selectTheStore.selectStore._id)
        .then((response) => {
            storeViewObject.value = response.data.data;
        })
        .finally(() => {
            setTimeout(() => {
                isLoading.value = false;
            }, 500);
        });

    EventAPI.getStoreEvent(selectTheStore.selectStore._id).then((response) => {
        eventCards.value = response.data.data;
    });
});
</script>

<style scoped lang="scss">
.sub-title_wrap {
    position: relative;

    .sub-title {
        position: relative;
        z-index: 2;
    }

    &::after {
        content: '';
        position: absolute;
        left: 0;
        bottom: 0px;
        width: 100%;
        border-bottom: 2px solid #d4d4d4;
    }
}

.store-introduction {
    background: linear-gradient(180deg, #fff6cc 0%, #ffffff 40%);
}
</style>
