<template>
    <div class="position-relative star_area">
        <div
            class="position-absolute empty_star"
            :style="{
                backgroundImage: 'url(' + starImg + ')',
            }"
        ></div>
        <div
            class="position-relative fill_star"
            :style="{
                backgroundImage: 'url(' + starImgFill + ')',
                width: width,
            }"
        ></div>
    </div>
</template>
<script setup>
import starImg from '@/assets/images/star.svg';
import starImgFill from '@/assets/images/star_fill.svg';

defineProps({
    width: { type: String, default: '' },
});
</script>
<style lang="scss" scoped>
.star_area {
    width: 100px;
    height: 20px;
    margin: 0 auto;
    position: relative;

    .empty_star {
        position: absolute;
        margin: 0;
        padding: 0;
        min-height: 20px;
        height: 100%;
        width: 100%;
        display: block;
        background-repeat: no-repeat;
    }

    .fill_star {
        margin: 0;
        padding: 0;
        min-height: 20px;
        height: 100%;
        display: block;
        overflow: hidden;
        background-repeat: no-repeat;
    }
}
</style>
