<script>
    import interact from 'interactjs'
    export default {
        name: 'zoom',
        data() {
            return {
                zoomRange: ['scale-100', 'scale-105', 'scale-125', 'scale-150', 'scale-[1.8]', 'scale-[2.0]', 'scale-[2.25]'],
                zoomCurrentRangeIndex: 0,
                zoomClass: 'scale-100',
                positionElement: { x: 0, y: 0 }
            }
        },
        methods: {
            zoomIn() {
                if (this.maxRange) return;
                this.zoomCurrentRangeIndex++;
                this.zoomClass = this.zoomRange[this.zoomCurrentRangeIndex];
            },
            zoomOut() {
                if (this.minRange) return;
                this.zoomCurrentRangeIndex--;
                this.zoomClass = this.zoomRange[this.zoomCurrentRangeIndex];
            },
            touchMoveHandler (event) {
                if (window.matchMedia("(min-width: 768px)").matches) {
                    const { dx, dy } = event;
                    this.positionElement.x += dx;
                    this.positionElement.y = dy;
                    this.$refs.zoomContent.scroll((this.$refs.zoomContent.scrollLeft - this.positionElement.x) / 2, (this.$refs.zoomContent.scrollTop - this.positionElement.y / 2));
                }
            }
        },
        computed: {
            maxRange () {
                return this.zoomCurrentRangeIndex === this.zoomRange.length - 1
            },
            minRange () {
                return this.zoomCurrentRangeIndex === 0
            }
        },
        mounted() {
            const self = this;
            interact('#zoom-target').draggable({
                listeners: {
                    move (event) {
                        self.touchMoveHandler(event);
                    }
                }
            })
        }
    }
</script>

<template>
    <div class="w-full h-full flex flex-col">
        <div ref="zoomContent" class="overflow-auto w-full max-h-full flex-1">
            <div id="zoom-target" class="touch-auto transform w-full" :class="zoomClass">
                <slot></slot>
            </div>
        </div>
        <div class="flex items-center justify-center gap-x-3 p-4 shrink-0">
            <button type="button" 
                class="w-12 h-12 rounded bg-indigo-500 hover:bg-indigo-700 border-0 focus:outline-none flex items-center justify-center text-white text-base disabled:bg-indigo-200" 
                :disabled="maxRange"
                @click="zoomIn">+</button>
            <button type="button" class="w-12 h-12 rounded bg-indigo-500 hover:bg-indigo-700 border-0 focus:outline-none flex items-center justify-center text-white text-base disabled:bg-indigo-200"
                :disabled="minRange"
                @click="zoomOut">-</button>
        </div>
    </div>
</template>