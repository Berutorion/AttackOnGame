<template>
    <div ref="modal" class="modal" tabindex="-1">
        <div class="modal-dialog modal-dialog-centered">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">{{ title }}</h5>
                    <button
                        type="button"
                        class="btn-close"
                        data-bs-dismiss="modal"
                        aria-label="Close"
                    ></button>
                </div>
                <div class="modal-body">
                    <p class="text-grey9F">已參加的活動</p>
                    <p class="fw-bold fs-4">{{ modalData?.title }}</p>
                    <div class="lh-lg">
                        <p class="fw-bold">活動日期：</p>
                        <p>
                            {{ modalData?.eventStartTime }} ~
                            {{ modalData?.eventEndTime }}
                        </p>
                        <p class="fw-bold">訂單編號：</p>
                        <p>{{ modalData?.idNumber }}</p>
                        <p class="fw-bold">NT$ {{ modalData?.totalAmount }}</p>
                    </div>

                    <!-- 店家名稱 但ＡＰＩ沒給～～ -->
                    <!-- <p class="fw-bold fs-4 text-center py-3">好玩工作室</p> -->
                    <p v-if="starRatingNum" class="text-center">
                        <span class="text-orange fw-bold">{{
                            starRatingNum
                        }}</span>
                        顆星
                    </p>
                    <div class="d-flex justify-content-center pb-4">
                        <div class="star-rating fs-4">
                            <input
                                id="sr-0-5"
                                v-model="starRatingNum"
                                type="radio"
                                name="star-rating-0"
                                value="5"
                            />
                            <label for="sr-0-5">★</label>
                            <input
                                id="sr-0-4"
                                v-model="starRatingNum"
                                type="radio"
                                name="star-rating-0"
                                value="4"
                            />
                            <label for="sr-0-4">★</label>
                            <input
                                id="sr-0-3"
                                v-model="starRatingNum"
                                type="radio"
                                name="star-rating-0"
                                value="3"
                            />
                            <label for="sr-0-3">★</label>
                            <input
                                id="sr-0-2"
                                v-model="starRatingNum"
                                type="radio"
                                name="star-rating-0"
                                value="2"
                            />
                            <label for="sr-0-2">★</label>
                            <input
                                id="sr-0-1"
                                v-model="starRatingNum"
                                type="radio"
                                name="star-rating-0"
                                value="1"
                            />
                            <label for="sr-0-1">★</label>
                        </div>
                    </div>
                    <textarea
                        id="comment"
                        v-model="content"
                        name="comment"
                        placeholder="請輸入評論"
                        class="form-control"
                        cols="10"
                        rows="5"
                    ></textarea>
                </div>
                <div class="modal-footer">
                    <button
                        type="button"
                        class="btn btn-secondary"
                        @click="myModalHide()"
                    >
                        取消
                    </button>
                    <button
                        type="button"
                        class="btn btn-primary"
                        @click="myModalHide()"
                    >
                        送出評論
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import Modal from 'bootstrap/js/dist/modal';
import PlayerAPI from '@/api/Player';
import useAlert from '@/stores/alert';

const AlertStore = useAlert();
const props = defineProps({
    title: { type: String, default: '' },
    modalData: { type: Object, default: () => ({}) },
});
const modal = ref(null);
const myModal = ref(null);
const starRatingNum = ref(null);
const content = ref('');
const postReview = async () => {
    const mydata = ref({
        orderNumber: props.modalData.idNumber,
        rate: starRatingNum.value,
        content: content.value,
    });
    try {
        const res = await PlayerAPI.postReview(mydata.value);
        console.log('Response', res);
        if (res.status === true) {
            AlertStore.openModal('success', '評論成功');
        }
    } catch (error) {
        const {
            response: { data },
        } = error;
        AlertStore.openModal('error', data.message);
    }
};

onMounted(() => {
    myModal.value = new Modal(modal.value);
});

const myModalShow = () => {
    myModal.value.show();
};

const myModalHide = () => {
    postReview();
    myModal.value.hide();
};

defineExpose({
    myModalShow,
    myModalHide,
});
</script>
<style lang="scss" scoped>
.star-rating {
    position: relative;
    display: inline-flex;
    flex-direction: row-reverse;
    justify-content: flex-end;
    margin: 0 -0.25rem;
}

input {
    position: absolute;
    opacity: 0;
}

label {
    cursor: pointer;
    color: grey;
    padding: 0 0.25rem;
    transition: color 0.15s;
}

input:checked ~ label {
    color: #ffdd33;
}

input:hover ~ label {
    color: goldenrod;
    transition: none;
}

label:active {
    color: darkgoldenrod !important;
}

input:focus-visible + label {
    outline-offset: 1px;
    outline: #4f46e5 solid 2px;
}
</style>
