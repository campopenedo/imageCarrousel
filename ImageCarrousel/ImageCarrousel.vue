<template>
    <section id="main-carrousel-visible">
        <section id="main-carrousel-rotate">
            <img v-for="(image, index) in images" :key=index :src="image.path">
        </section>
            <CarrouselButtons v-if="!singleImage" v-on:image-rotation-to="carrouselMovement" ref="carrouselMovementButtons"></CarrouselButtons>
            <imagePositionPoints v-if="!singleImage" :images-number="numberOfImages" ref="carrouselPositionPoints"></imagePositionPoints>
    </section>
</template>

<script>
import CarrouselButtons from "./CarrouselButtons.vue";
import imagePositionPoints from "./ImagePositionPoints.vue";
//TODO: refactorizar funciones. Functions must be small. La mayoría de las funciones deben de ser de 1 línea.


//Ideas: un ordenador a la derecha, y escribiendo código, y en un momento comienza a poner texto aparece en la misma web. Juego entre gif y css, o incluso ambos textos y que si recoges ese texto, puedas reproducirlo en otro sitio
//otra idea: una nave espacial, con una ventana donde se ve la tierra. Cosas flotando, cada cosa es un link a la página, gitlab, blog, cs
    export default {
    data() {
        return {
            //TODO: poner alt descriptions y comenzar con hacer que sea un carrousel infinito. Adaptar el estilo a diferentes dispositivos(pc, tablet, móvil)
            //esto se devuelve del backend posicionado, todo en bbdd, con alt descriptions y demás. Importante la posición para poder decir si es el primero y poner o no el puntero
            images: {
                17: {
                    path: require("./../../../public/youngManCoding.jpg")
                },
                18: {
                    path: require("./../../../public/romb-colours-black.png")
                },
                19: {
                    path: require("./../../../public/youngManCoding.jpg")
                },
                20: {
                    path: require("./../../../public/youngManCoding.jpg")
                },
                21: {
                    path: require("./../../../public/youngManCoding.jpg")
                },

            },
            singleImage: false,
            twoImages: false,
            imagesContainer: "",
            numberOfImages: 0,
            carrouselInMovement: false,
            touchPadProperties: {
                startScreenX: 0
            }
        };
    },
    components: { CarrouselButtons, imagePositionPoints },
    methods: {

        orderObjectByImageKeyPosition(images) {
            return Object.keys(images).sort().reduce(function (result, key) {
                result[key] = images[key];
                return result;
            }, {});
        },

        orderCarrouselInitialPosition() {
            if(this.numberOfImages === 1) {
                this.orderCarrouselSingleImage();
            }
            else if (this.numberOfImages >= 2) {
                this.orderCarrouselMultipleImages(); 
            }
        },

        orderCarrouselSingleImage() {
            this.singleImage = true;
            this.imagesContainer.children[0].classList.add("single-carrousel-image");
        },

        orderCarrouselMultipleImages() {
            let lastImageKey = Object.keys(this.images)[Object.keys(this.images).length - 1]
            
            this.orderObjectByImageKeyPosition(this.images);
            this.images['0'] = this.images[lastImageKey];

            if(this.numberOfImages >= 3)
            {
                delete this.images[lastImageKey]
            } else {
                this.twoImages = true;
            }

            this.initialImagesClasses();
        },

        initialImagesClasses() {
           document.addEventListener("DOMContentLoaded", () => { 
                this.imagesContainer.children[0].classList.add("firstCarrouselImage");
                    this.imagesContainer.children[1].classList.add("visible-image");    
                this.imagesContainer.children[this.imagesContainer.children.length - 1].classList.add("lastCarrouselImage");
                for(let i = 0; i < this.imagesContainer.children.length; i++) {
                    this.imagesContainer.children[i].setAttribute('data-position', i)
                }
            });
        },

        carrouselMovement(carrouselDirection) {
            this.imagesRotationTo(carrouselDirection);
            this.$refs.carrouselPositionPoints.changeVisiblePoint(carrouselDirection);
        },

        imagesRotationTo(carrouselDirection) {
            let indexImage;

            if(carrouselDirection === 'right') {
                indexImage = document.getElementsByClassName('firstCarrouselImage')[0].getAttribute('data-position');
                this.rotation(carrouselDirection, indexImage);

            } else if(carrouselDirection === 'left') {
                indexImage = document.getElementsByClassName('lastCarrouselImage')[0].getAttribute('data-position');
                this.rotation(carrouselDirection, indexImage);
            }
        },

        rotation(carrouselDirection, indexImage) {
            let leftDisplacement = 0,
                images = this.imagesContainer.children,
                finalLeftDisplacement = carrouselDirection === 'right' ? -100 : 100,
                everyIntervalLeftDisplacement = carrouselDirection === 'right' ? -2 : 2;

            this.carrouselInMovement = true;
            let movement = setInterval(() => {
                if(leftDisplacement !== finalLeftDisplacement) 
                {
                    leftDisplacement = leftDisplacement + everyIntervalLeftDisplacement;
                    this.continueMovement(everyIntervalLeftDisplacement, images);
                }
                else
                {
                    this.endMovement(movement, indexImage, carrouselDirection);
                }
            }, 10);
        },

        endMovement(movement, indexImage, carrouselDirection) {
            this.changeEndpointsImagesPosition(indexImage, carrouselDirection);
            clearInterval(movement);
            this.carrouselInMovement = false;
        },

        continueMovement(everyIntervalLeftDisplacement, images) {
            for(let i = 0; i < images.length; i++)
            { 
                if(!images[i].style.left) {
                    images[i].style.left = '0vw';
                }
                images[i].style.left = `${parseInt(images[i].style.left.split('vw')[0]) + everyIntervalLeftDisplacement}vw`;
            }
        },

        changeEndpointsImagesPosition(indexImage, carrouselDirection) {
            let images = this.imagesContainer.children,
                imagePosition = parseInt(indexImage) + 1;
                
            let imageOnTheLeft = document.getElementsByClassName('firstCarrouselImage')[0],
                imageOnTheRight = document.getElementsByClassName('lastCarrouselImage')[0];

            if(carrouselDirection === 'right') {
                let imagesToEndIteration = images.length - imagePosition;
                imageOnTheLeft.style.left = `${imagesToEndIteration * 100}vw`;
                if(imageOnTheLeft.nextElementSibling !== null) {
                    imageOnTheLeft.nextElementSibling.classList.add('firstCarrouselImage');
                    imageOnTheLeft.classList.remove('firstCarrouselImage');                    
                } else {
                    this.imagesContainer.children[0].classList.add('firstCarrouselImage');
                    imageOnTheLeft.classList.remove('firstCarrouselImage');
                    
                }

                imageOnTheRight.classList.remove('lastCarrouselImage');
                imageOnTheLeft.classList.add('lastCarrouselImage');
            } else {
                imageOnTheRight.style.left = `-${indexImage * 100}vw`;
                
                imageOnTheRight.classList.add('firstCarrouselImage');
                imageOnTheRight.classList.remove('lastCarrouselImage');
                
                imageOnTheLeft.classList.remove('firstCarrouselImage');

                if(imageOnTheRight.previousElementSibling !== null) {
                    imageOnTheRight.previousElementSibling.classList.add('lastCarrouselImage');
                } else {
                    images[images.length - 1].classList.add('lastCarrouselImage');
                }
            }
            this.changeVisibleImageClass();
        },
        
        changeVisibleImageClass() {
            document.getElementsByClassName('visible-image')[0].classList.remove('visible-image');
            if(document.getElementsByClassName('firstCarrouselImage')[0].nextElementSibling !== null) {
                document.getElementsByClassName('firstCarrouselImage')[0].nextElementSibling.classList.add('visible-image');
            } else {
                this.imagesContainer.children[0].classList.add('visible-image');
            }
        },

        touchpadMovement() {
            document.getElementById('main-carrousel-rotate').addEventListener('touchstart', (e) => this.getTouchpadStartPoint(e));

            document.getElementById('main-carrousel-rotate').addEventListener('touchend', (e) => {
                if(!this.carrouselInMovement) {
                    let carrouselDirection = this.getCarrouselDirectionInTouchpad(e);
                    this.carrouselMovement(carrouselDirection);
                }
            });
        },

        getTouchpadStartPoint(firstTouch) {
            this.touchPadProperties.startScreenX = firstTouch.changedTouches[0].screenX;
        },

        getCarrouselDirectionInTouchpad(touchEvent) {
            return this.touchPadProperties.startScreenX - touchEvent.changedTouches[0].screenX < 0 ? 'left' : 'right';
        }

        
    },

    mounted() {
        this.imagesContainer = document.getElementById("main-carrousel-rotate");
        this.numberOfImages = Object.keys(this.images).length;

        this.orderCarrouselInitialPosition();

        this.touchpadMovement();
    },
    watch: {
        carrouselInMovement: function () {
            this.$refs.carrouselMovementButtons.toggleDisabledButtons();
        }
    }
}
</script>

<style scoped>
    img {
        width: 100vw;
        max-width: 100vw;
        max-height: 45vw;
        position: relative;
    }
    
    section#main-carrousel-rotate {
        display: inline-flex;
        position: relative;
        left: -100vw;
    }

    section#main-carrousel-visible {
        width: 100vw;
        max-width: 100%;
        height: 45vw;
        overflow: hidden;
        z-index: 5;
        padding-top: 70px;
        background-color: black;
    }

    .single-carrousel-image {
        position:relative;
        left: 100vw;
    }

    @media screen and (max-width: 1024px) {
        section#main-carrousel-visible {
            padding-top: 70px;
        }

        img {
            width: 100vw;
            padding-left: 0vw;
            padding-right: 0vw;
        }
    }


</style>