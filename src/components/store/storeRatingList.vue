<template>
    <div>
        <div class="sub-title_wrap mb-3">
            <h2
                class="fs-6 text-primary fw-bold pb-2 border-bottom border-2 border-primary fz-6 d-inline-block sub-title mb-0"
            >
                店家評價
            </h2>
        </div>
        <div
            v-if="reviewData === null"
            class="bg-warning bg-opacity-10 p-4 border-2 border border-warning mb-3"
        >
            <p class="text-center">尚未有人評價店家</p>
        </div>
        <div
            v-if="reviewData !== null"
            class="bg-warning bg-opacity-10 p-4 border-2 border border-warning d-flex align-items-center gap-4 mb-3"
        >
            <div class="text-center">
                <p class="fw-bold">
                    <span class="fs-5">{{ reviewData[0].rate }}</span
                    >/5
                </p>
                <star :width="calculatePercentage(reviewData[0].rate)"></star>
            </div>
            <div
                class="btn-group gap-3"
                role="group"
                aria-label="Basic radio toggle button group"
            >
                <input
                    id="starBtn1"
                    type="radio"
                    class="btn-check"
                    name="starBtn"
                    autocomplete="off"
                    checked
                />
                <label class="btn btn-outline-primary rounded" for="starBtn1"
                    >全部</label
                >

                <input
                    id="starBtn2"
                    type="radio"
                    class="btn-check"
                    name="starBtn"
                    autocomplete="off"
                />
                <label class="btn btn-outline-primary rounded" for="starBtn2"
                    >五星</label
                >

                <input
                    id="starBtn3"
                    type="radio"
                    class="btn-check"
                    name="starBtn"
                    autocomplete="off"
                />
                <label class="btn btn-outline-primary rounded" for="starBtn3"
                    >四星</label
                >
                <input
                    id="starBtn4"
                    type="radio"
                    class="btn-check"
                    name="starBtn"
                    autocomplete="off"
                />
                <label class="btn btn-outline-primary rounded" for="starBtn4"
                    >三星</label
                >
                <input
                    id="starBtn5"
                    type="radio"
                    class="btn-check"
                    name="starBtn"
                    autocomplete="off"
                />
                <label class="btn btn-outline-primary rounded" for="starBtn5"
                    >二星</label
                >
                <input
                    id="starBtn6"
                    type="radio"
                    class="btn-check"
                    name="starBtn"
                    autocomplete="off"
                />
                <label class="btn btn-outline-primary rounded" for="starBtn6"
                    >一星</label
                >
            </div>
        </div>
        <ul v-if="reviewData !== null" class="mb-3 p-0">
            <li
                v-for="value in reviewData[0].content"
                :key="value._id"
                class="d-flex gap-3 p-3 border-bottom"
            >
                <div
                    style="width: 48px; height: 48px; overflow: hidden"
                    class="rounded-circle"
                >
                    <img
                        class="object-fit-cover w-100 h-100"
                        :src="value.author.avatar"
                        alt=""
                    />
                </div>
                <div class="flex-grow-1">
                    <p class="fw-bold text-primary fs-6 inter">
                        {{ value.author.name }}
                    </p>
                    <p>{{ value.content }}</p>
                    <p class="text-grey9F fs-10">
                        {{ formatDate(value.createTime) }}
                    </p>
                </div>
                <star :width="calculatePercentage(value.rate)"></star>
            </li>
        </ul>
        <Pagination
            v-if="reviewData !== null"
            :current-page="currentPage"
            :total-pages="totalPages"
            @update:currentPage="handlePageChange"
        >
        </Pagination>
    </div>
</template>
<script setup>
import { onMounted, ref, computed } from 'vue';
import star from '@/components/common/starRating.vue';
import Pagination from '@/components/common/Pagination.vue';
import StoreAPI from '@/api/Store';
import { selectStoreData } from '@/stores/selectStore';

const currentPage = ref(1);
const totalPages = computed(() => {
    return 1;
    // return Math.ceil(rawEventData.value.length / PER_PAGE);
});
function handlePageChange(newPage) {
    currentPage.value = newPage;
}
function calculatePercentage(stars) {
    return `${(stars / 5) * 100}%`;
}
function formatDate(isoDateString) {
    const date = new Date(isoDateString);

    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    const seconds = String(date.getSeconds()).padStart(2, '0');

    return `${year}-${month}-${day} ${hours}:${minutes}:${seconds}`;
}
const reviewData = ref(null);
const selectTheStore = selectStoreData();
const getReviewData = async (storeId) => {
    await StoreAPI.getReview(storeId).then((res) => {
        reviewData.value = res.data.data;
        console.log(reviewData.value);
    });
};

onMounted(() => {
    getReviewData(selectTheStore.selectStore._id);
});
</script>
<style lang="scss" scoped>
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

.btn-outline-primary {
    background-color: #ffff;
    color: #333333;
    border: 1px solid #d4d4d4;
}

.btn-outline-primary:hover {
    background-color: #0088cc;
    color: #ffff;
}
</style>
