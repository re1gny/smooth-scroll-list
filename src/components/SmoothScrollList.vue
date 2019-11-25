<template>
<div
    class="scroll-list"
    ref="list"
>
    <div
        class="scroll-list__scrolled-content"
        :style="{ transform: 'translate3d(' + offset + 'px, 0px, 0px)' }"
    >
        <ScrollItem
            v-for="item in items"
            :key="item.id"
            :data="item"
            :active="active"
            @select="handleSelect"
        />
    </div>
</div>
</template>

<script>
import ScrollItem from '@/components/SmoothScrollItem';

export default {
    name: "SmothScrollList",

    components: {
        ScrollItem,
    },

    props: {
        anchor: {
            type: Number,
            required: true,
        }
    },

    data() {
        return {
            active: 2,
            scrollBuffer: [],
            childrenOffsets: {},
            isScrolling: false,
            listenerId: null,
            lastScrollExtreme: null,
            offset: 0,
            delayTimeoutId: null,
            scrollings: [],
            items: [
                {
                    id: 0,
                    title: 'Lorem ipsum',
                    description: 'Some description of the cart'
                },
                {
                    id: 1,
                    title: 'Lorem ipsum',
                    description: 'Some description of the cart'
                },
                {
                    id: 2,
                    title: 'Lorem ipsum',
                    description: 'Some description of the cart'
                },
                {
                    id: 3,
                    title: 'Lorem ipsum',
                    description: 'Some description of the cart'
                },
                {
                    id: 4,
                    title: 'Lorem ipsum',
                    description: 'Some description of the cart'
                },
                {
                    id: 5,
                    title: 'Lorem ipsum',
                    description: 'Some description of the cart'
                },
                {
                    id: 6,
                    title: 'Lorem ipsum',
                    description: 'Some description of the cart'
                }
            ]
        }
    },

    computed: {
        filteredScrollings() {
            return this.splitArray(this.scrollings.reduce((result, current) => {
                return result[result.length - 1] === current ?
                    result
                    : [
                        ...result,
                        current
                    ];
            }, []), 2).map(item => Math.hypot(...item));
        },
    },

    watch: {
        scrollBuffer(value) {
            console.log(value);
        },
    },

    created() {
        this.handleThrottledScroll = this.throttle(this.handleScroll, 600);
    },

    mounted() {
        this.calculateChildrenOffsets();
        this.handleSelect(this.active);
        this.addWheelListener();
    },

    methods: {
        addWheelListener() {
            this.$refs.list.addEventListener('wheel', this.handleScroll);
            this.listenerId = Math.random();
        },

        removeWheelListener() {
            this.$refs.list.removeEventListener('wheel', this.handleScroll);
            this.listenerId = null;
        },

        pushScrolling(scrolling) {
            if (this.scrollings.length === 150) this.scrollings.shift();
            this.scrollings.push(scrolling);
        },

        clearScrollings() {
            this.scrollings = [];
        },

        handleSelect(value) {
            if (value < 0 || value >= this.items.length) return;
            this.active = value;
            this.offset = this.anchor - this.childrenOffsets[value];
        },

        handleScroll(e) {
            // if (this.isScrolling) return;
            // this.delayTimeoutId = clearTimeout(this.delayTimeoutId);

            const currentListenerId = this.listenerId;

            e = e ? e : window.event;
            const delta = e.deltaX;
            // console.log(delta)
            if (!delta) return;

            this.pushScrolling(delta);
            const extreme = this.getScrollExtreme();

            // console.log(exteme);
            if (extreme !== null && extreme !== this.lastScrollExtreme) {
                const direction = delta > 0 ? 1 : -1;
                this.handleSelect(this.active - direction);
                this.lastScrollExtreme = extreme;
                this.clearScrollings();
            }
            this.isScrolling = true;
            // this.removeWheelListener();
            // setTimeout(this.addWheelListener, 600);
            // this.delayTimeoutId = setTimeout(this.clearScrolling, 200);
        },

        throttle(fn, wait) {
            let time = Date.now();
            return function() {
                if ((time + wait - Date.now()) < 0) {
                    fn();
                    time = Date.now();
                }
            }
        },

        calculateChildrenOffsets() {
            this.$children.forEach((children) => {
                const currentEl = children.$el;
                const elIndex = children.$props.data.id;
                const elWidth = currentEl.offsetWidth;
                const elCenter = elWidth / 2;
                const elOffset = (elIndex * elWidth) + (elIndex * 18) + elCenter;
                this.$set(this.childrenOffsets, elIndex, elOffset);
            });
        },

        splitArray(list, chunkSize) {
            return [].concat.apply([],
                list.map(function(item, i) {
                    return i % chunkSize ? [] : [list.slice(i, i + chunkSize)];
                })
            );
        },

        getScrollExtreme() {
            let extreme = null;
            const currentScroll = this.filteredScrollings[this.filteredScrollings.length - 3];
            const prevScroll = this.filteredScrollings[this.filteredScrollings.length - 4];
            const nextScroll = this.filteredScrollings[this.filteredScrollings.length - 2];

            if(currentScroll > prevScroll && currentScroll > nextScroll) {
                console.log(prevScroll, currentScroll, nextScroll)

                extreme = currentScroll;
            }

            return extreme;
        },
    },
}
</script>

<style lang="scss" scoped>
.scroll-list {
    width: calc(100% + 40px);
    position: relative;
    height: 280px;
    margin-left: -20px;
    overflow: hidden;
    padding-bottom: 10px;

    &__scrolled-content {
        display: flex;
        /*position: absolute;*/
        /*top: 0;*/
        /*left: 0;*/
        transition: transform 0.6s cubic-bezier(0.645, 0.045, 0.355, 1);
        will-change: transform;
    }
}
</style>
