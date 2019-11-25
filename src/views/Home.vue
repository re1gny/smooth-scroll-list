<template>
    <div class="home">
        <div class="home__anchor"></div>
        <ScrollList
            :anchor="360"
        />
    </div>
</template>

<script>
import ScrollList from '@/components/SmoothScrollList';

export default {
    name: 'home',
    components: {
        ScrollList,
    }
}
</script>


<style lang="scss" scoped>
.home {
    position: relative;
    padding: 80px 20px 60px;

    &__anchor {
        position: absolute;
        top: 20px;
        left: 360px;
        width: 1px;
        height: calc(100vh - 40px);
        background-color: #eceaee;
    }
}
</style>
