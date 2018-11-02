<template>
    <div v-show="show" :transition="transition">
        <div class="modal show" role="dialog" @click.self="clickMask">
            <div class="modal-dialog" :class="modalClass" ref="dialog">
                <div class="modal-content">
                    <!--Header-->
                    <div class="modal-header">
                        <slot name="header">
                            <h3 class="modal-title">
                                <slot name="title">
                                    {{title}}
                                </slot>
                            </h3>
                            <button @click="cancel" type="button" class="close" aria-label="Close"><i class="fal fa-times"></i></button>
                        </slot>
                    </div>
                    <!--Container-->
                    <div class="modal-body">
                        <slot></slot>
                    </div>
                    <!--Footer-->
                    <div class="modal-footer">
                        <slot name="footer">
                            <button v-if="!cancelHidden" type="button" :class="cancelClass" @click="cancel">
                                <i class="fal fa-times"></i>
                                {{cancelText}}
                            </button>
                            <button v-if="!okHidden" type="button" :class="okClass" @click="ok">
                                <i class="fal fa-check"></i>
                                {{okText}}
                            </button>
                        </slot>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal-backdrop show"></div>
    </div>
</template>

<script>
    /**
     * Bootstrap Style Modal Component for Vue
     * Depend on Bootstrap.css
     */

    export default {
        props: {
            show: {
                type: Boolean,
                twoWay: true,
                default: false
            },
            title: {
                type: String,
                default: 'Modal'
            },
            small: {
                type: Boolean,
                default: false
            },
            large: {
                type: Boolean,
                default: false
            },
            full: {
                type: Boolean,
                default: false
            },
            force: {
                type: Boolean,
                default: false
            },
            transition: {
                type: String,
                default: 'modal'
            },
            okText: {
                type: String,
                default: 'Save'
            },
            cancelText: {
                type: String,
                default: 'Cancel'
            },
            okClass: {
                type: String,
                default: 'btn btn-primary'
            },
            cancelClass: {
                type: String,
                default: 'btn btn-default'
            },
            okHidden: {
                type: Boolean,
                default: false
            },
            cancelHidden: {
                type: Boolean,
                default: false
            }
        },
        data () {
            return {
                duration: null
            };
        },
        computed: {
            modalClass () {
                return {
                    'modal-lg': this.large,
                    'modal-sm': this.small,
                    'modal-full': this.full
                }
            }
        },
        created () {
            if (this.show) {
                document.body.className += ' modal-open';
            }
        },
        beforeDestroy () {
            document.body.className = document.body.className.replace(/\s?modal-open/, '');
        },
        watch: {
            show (value) {
                if (value) {
                    document.body.className += ' modal-open';
                }
                else {
                    if (!this.duration) {
                        this.duration = window.getComputedStyle(this.$refs.dialog)['transition-duration'].replace('s', '') * 1000;
                    }

                    window.setTimeout(() => {
                        document.body.className = document.body.className.replace(/\s?modal-open/, '');
                    }, this.duration || 0);
                }
            }
        },
        methods: {
            ok () {
                this.$emit('ok');
            },
            cancel () {
                this.$emit('cancel');
            },
            clickMask () {
                if (!this.force) {
                    this.cancel();
                }
            }
        }
    };
</script>

<style scoped>
    .modal {
        display: block;
    }
    .modal-transition {
        transition: all .6s ease;
    }
    .modal-leave {
        border-radius: 1px !important;
    }
    .modal-transition .modal-dialog, .modal-transition .modal-backdrop {
        transition: all .5s ease;
    }
    .modal-enter .modal-dialog, .modal-leave .modal-dialog {
        opacity: 0;
        transform: translateY(-30%);
    }
    .modal-enter .modal-backdrop, .modal-leave .modal-backdrop {
        opacity: 0;
    }
    .modal-title {
        color: #2b343f;
        margin: 0;
    }
    .modal-footer button i {
        padding-right: 5px;
    }
</style>
