<template>
    <section id="carrousel-buttons">
        <button class="arrow-key-left" @click="changeImageTo('left')"></button>
        <button class="arrow-key-right" @click="changeImageTo('right')"></button>
   </section>
</template>

<script>
    export default {
        props: {
            arrowType: String,
        },

        data() {
            return {
                carrouselRotate: "",
                images: []
            }
        },

        mounted() {
            this.images = [...document.getElementById('main-carrousel-rotate').children]
        },

        methods: {

            changeImageTo(carrouselDirection) {
                this.$emit('imageRotationTo',carrouselDirection);
            },

            toggleDisabledButtons() {
                let arrayButtons = [...document.getElementById('carrousel-buttons').children];
                for(let i = 0; i < arrayButtons.length; i++) {
                    arrayButtons[i].disabled = !arrayButtons[i].disabled;
                }
            },
            
        },
    }
</script>

<style>
    #carrousel-buttons {
        width: 100vw;
        position: relative;
        height: 0;
        float: left;
        top: -25vw;
    }

    .arrow-key-left, .arrow-key-right{
        position: relative;
        height: 5vw;
        z-index: 4;
    }

    .arrow-key-left {
        float: left;
        left: 2vw;
    }
    
    .arrow-key-left::before{
        content: "<";
        font-size: 3vw;
        font-weight: bold;
        color: rgb(82, 82, 82);
    }

    .arrow-key-right {
        float: right;
        right: 2vw;
    }

    .arrow-key-right::before{
        content: "/>";
        font-size: 3vw;
        font-weight: bold;
        color: rgb(82, 82, 82);
    }
    
    @media (pointer: coarse) {
        .arrow-key-left, .arrow-key-right{
            display: none;
        }
    }

</style>