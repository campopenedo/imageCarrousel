<template>
<section id="carrousel-counter-points">
    <div v-for="n in imagesNumber" :key="n" class="carrousel-counter-point">
        <div :class="counterInitialImage(n)" :data-position="n - 1"></div>
    </div>
</section>
</template>

<script>
export default {
    props: {
        imagesNumber: Number
    },

    methods: {
        counterInitialImage(numberOfImage) {
            let classesOfImage = "";
            if(numberOfImage === 1) {
                classesOfImage += "counter-visible-image";
            }
            return classesOfImage;
        },
        changeVisiblePoint(carrouselDirection) {
            let nextPosition = this.nextVisiblePointPosition(carrouselDirection);
            this.newVisiblePoint(nextPosition);
            this.changePointAnimation(carrouselDirection, nextPosition);
        },

        nextVisiblePointPosition(carrouselDirection) {
            let position;
            if(carrouselDirection === 'right') {
                position = parseInt(document.getElementsByClassName('counter-visible-image')[0].getAttribute('data-position')) + 1;
            } else {
                position = parseInt(document.getElementsByClassName('counter-visible-image')[0].getAttribute('data-position')) - 1;
            }

            if(position === - 1) {
                position = parseInt(document.getElementById('carrousel-counter-points').children.length) - 1;
            } else if(position === document.getElementById('carrousel-counter-points').children.length) {
                position = 0;
            }

            return position;
        },

        newVisiblePoint(nextPosition) {
            document.querySelector('.counter-visible-image').classList.remove('counter-visible-image');
            document.getElementById('carrousel-counter-points').children[nextPosition].children[0].classList.add('counter-visible-image');
        },

        changePointAnimation(carrouselDirection, nextPosition) {
            let animationData = this.getAnimationData(carrouselDirection, nextPosition);
            this.animationDataManipulation(animationData, nextPosition);
        },

        getAnimationData(carrouselDirection, nextPosition) {
            let maxDataPositionValue = document.getElementById('carrousel-counter-points').children.length - 1;
            
            let animationData = {
                lastPointToFirstPoint: false,
                firstPointToLastPoint: false,
                intermediatePoints: false,
                addedClassesForAnimation: []
            };

            animationData.lastPointToFirstPoint = nextPosition === 0 && carrouselDirection === 'right' ? true : false;
            animationData.firstPointToLastPoint = nextPosition === maxDataPositionValue && carrouselDirection === 'left' ? true : false;
            animationData.intermediatePoints = animationData.lastPointToFirstPoint === false && animationData.firstPointToLastPoint === false ? true : false;

            if(animationData.firstPointToLastPoint || animationData.lastPointToFirstPoint) {
                animationData.addedClassesForAnimation.push('endpoints-end');
                animationData.addedClassesForAnimation.push('endpoints-beginning');
            } else if(animationData.intermediatePoints) {
                animationData.addedClassesForAnimation.push(carrouselDirection)
            }

            return animationData;
        },

        animationDataManipulation(animationData, nextPosition) {
            this.setAnimationData(animationData, nextPosition);

            setTimeout(() =>{
                this.deleteClassesAnimation(animationData.addedClassesForAnimation);
            }, 1000)
        },

        setAnimationData(animationData, nextPosition) {
            document.getElementById('carrousel-counter-points').children[nextPosition].children[0].classList.add(animationData.addedClassesForAnimation[0]);

            if(animationData.firstPointToLastPoint) {
                document.getElementById('carrousel-counter-points').children[0].children[0].classList.add(animationData.addedClassesForAnimation[1]);
            } else if(animationData.lastPointToFirstPoint) {
                document.getElementById('carrousel-counter-points').children[parseInt(document.getElementById('carrousel-counter-points').children.length) -1].children[0].classList.add(animationData.addedClassesForAnimation[1]);
            }
        },

        deleteClassesAnimation(nameOfClasses) {
            for(let i = 0; i < nameOfClasses.length; i++) {
                document.querySelector('#carrousel-counter-points').querySelector(`.${nameOfClasses[i]}`).classList.remove(`${nameOfClasses[i]}`);
            }
        },
        
    }
}
</script>
<style scoped>

    #carrousel-counter-points {
        width: 100vw;
        display: flex;
        height: 2vw;
        justify-content: center;
        position: relative;
    }
    .carrousel-counter-point {
        width: 1vw;
        height: 1vw;
        margin: .5vw;
        border-style: solid;
        border-width: 2px;
        border-color: rgba(48, 128, 139, 0.75);
    }

    .counter-visible-image {
        width: 100%;
        height: 100%;
        background-color: rgba(35, 68, 101, 0.75);
        box-shadow: 0 0 13px rgba(48, 128, 139);
    }

    .counter-visible-image.right {
        animation-name: movementRight;
        animation-duration: var(--seconds);
        transition-timing-function: linear;
    }

    .counter-visible-image.left {
        animation-name: movementLeft;
        animation-duration: .5s;
        transition-timing-function: linear;
    }

    .endpoints-beginning{
        animation-name: movementBeginning;
        animation-duration: .5s;
        transition-timing-function: linear;
    }

    .counter-visible-image.endpoints-end {
        animation-name: movementEnd;
        animation-duration: .5s;
        transition-timing-function: linear;
    }

    @media only screen and (max-width: 1024px) {
        .carrousel-counter-point {
            width: 10px;
            height: 10px;
        }

        #carrousel-counter-points {
            height: 18px;
            margin: .5vw;
        }

        @keyframes movementRight {
            0% {
                position: relative;
                left: calc(-10px - .5vw);
                box-shadow: none;
            }
            100% {
                position: relative;
                left: 0px;
                box-shadow: 0 0 10px rgba(48, 128, 139);
            }
        }

        @keyframes movementLeft {
            0% {
                position: relative;
                left: calc(10px + .5vw);
                box-shadow: none;
            }
            100% {
                position: relative;
                left: 0px;
                box-shadow: none;
                box-shadow: 0 0 10px rgba(48, 128, 139);
            }
        }
    }

    @keyframes movementRight {
        0% {
            position: relative;
            left: calc(-2vw);
            box-shadow: none;
        }
        100% {
            position: relative;
            left: 0px;
            box-shadow: 0 0 10px rgba(48, 128, 139);
        }
    }

    @keyframes movementLeft {
        0% {
            position: relative;
            left: calc(2vw);
            box-shadow: none;
        }
        100% {
            position: relative;
            left: 0px;
            box-shadow: none;
            box-shadow: 0 0 10px rgba(48, 128, 139);
        }
    }

    @keyframes movementEnd {
        0% {
            background-color:  rgba(142, 222, 220, 0.5);
            position: relative;
            width: 0%;
            height: 0%;
            top: 50%;
            left: 50%;
        }
        100% {
            position: relative;
            width: 100%;
            height: 100%;
            top: 0%;
            left: 0%;
        }
    }

    @keyframes movementBeginning {
        0% {
            background-color:  rgba(142, 222, 220, 0.5);
            position: relative;
            width: 100%;
            height: 100%;
            top: 0%;
            left: 0%;
        }
        100% {
            position: relative;
            width: 0%;
            height: 0%;
            top: 50%;
            left: 50%;
        }
    }

</style>