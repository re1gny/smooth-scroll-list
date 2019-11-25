<template>
<div
    class="scroll-item"
    :class="{ active: data.id === active }"
    @click="$emit('select', data.id)"
>
    <div class="scroll-item__head">
        <span class="scroll-item__number">
            {{ data.id }}
        </span>
        <span class="scroll-item__title">
            {{ data.title }}
        </span>
    </div>

    <div class="scroll-item__body">
        <span class="scroll-item__description">
            {{ data.description }}
        </span>
    </div>
</div>
</template>

<script>
export default {
    name: "SmoothScrollItem",

    props: {
        data: {
            type: Object,
            default: () => ({}),
        },

        active: {
            type: Number,
            default: null,
        }
    }
}
</script>

<style lang="scss" scoped>
.scroll-item {
    width: 220px;
    min-width: 220px;
    height: 280px;
    background-color: #ffffff;
    color: #303b53;
    border-radius: 46px;
    padding: 32px;
    box-sizing: border-box;
    box-shadow: 0px 3px 10px -4px rgba(0,0,0,0.2);
    cursor: pointer;
    transition: 0.6s cubic-bezier(0.645, 0.045, 0.355, 1);

    &.active {
        background-color: lighten(#333bd6, 10%);
        color: #eceaee;
        box-shadow: 0px 3px 10px -4px rgba(#333bd6 ,0.2);
    }

    & + & {
        margin-left: 18px;
    }

    &__head {
        display: flex;
        align-items: center;
    }

    &__body {
        margin-top: 10px;
    }

    &__number {
        font-size: 36px;
    }

    &__title {
        font-size: 20px;
        margin-left: 12px;
    }
}
</style>
