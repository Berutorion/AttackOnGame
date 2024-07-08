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
            class="bg-warning bg-opacity-10 p-4 border-2 border border-warning mb-3"
        >
            <p class="text-center">尚未有人評價店家</p>
        </div>
        <div
            class="bg-warning bg-opacity-10 p-4 border-2 border border-warning d-flex align-items-center gap-4 mb-3"
        >
            <div class="text-center">
                <p class="fw-bold"><span class="fs-5">4.5</span>/5</p>
                <star width="70%"></star>
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
        <ul class="mb-3 p-0">
            <li class="d-flex gap-3 p-3 border-bottom">
                <div
                    style="width: 48px; height: 48px; overflow: hidden"
                    class="rounded-circle"
                >
                    <img
                        class="object-fit-cover w-100 h-100"
                        src="https://images.unsplash.com/photo-1720010944710-01161d017608?w=800&auto=format&fit=crop&q=60&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxmZWF0dXJlZC1waG90b3MtZmVlZHwxNXx8fGVufDB8fHx8fA%3D%3D"
                        alt=""
                    />
                </div>
                <div class="flex-grow-1">
                    <p class="fw-bold text-primary fs-6 inter">BABY SHAR</p>
                    <p>場地舒服～老闆人超好！！！！</p>
                    <p class="text-grey9F fs-10">2024-03-22 11:00</p>
                </div>
                <star width="70%"></star>
            </li>
        </ul>
        <Pagination
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

const currentPage = ref(1);
const totalPages = computed(() => {
    return 3;
    // return Math.ceil(rawEventData.value.length / PER_PAGE);
});
function handlePageChange(newPage) {
    currentPage.value = newPage;
}

onMounted(() => {});
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
