<template>


<div class="plugin-helios-header-footer">
    <div id="helios-header" class="helios-special-elements">
        <nav class="top-nav" data-fplugin="expand-menu" data-fres='["md", "lg"]' data-fopts='{"triggerSelector":".dropdown", "containerSelector":".expand-container", "grouped" : true}'>
            <nav class="top-nav">
                <ul class="module-container">
                    
                    <li v-for="(topMenuItem, topMenuIndex) in topNavigation" :key="topMenuIndex" :class="[topMenuIndex == 0 ? 'home' : 'text-uppercase']">
                        <a v-if="topMenuIndex === 0"  class="gtm-button home_link" :data-phf-ico-before="topMenuItem.icon" data-tracking-name="Nav0_Home" data-tracking-location="Menu top navigation" :href="topMenuItem.url"></a>
                        <a v-else :data-tracking-name="topNavIndexClass" data-tracking-location="Menu top navigation" class="gtm-button" :href="topMenuItem.url" title="" target="_self" rel="nofollow noreferrer">
                            <span>{{ topMenuItem.title }}</span>
                        </a>
                    </li>
                    
                </ul>
            </nav>
        </nav>

        <div data-fplugin="expand-menu" data-fopts='{"triggerSelector":".dropdown", "containerSelector":".expand-container", "grouped" : true}'>
            <div class="page-header--helios" data-fplugin="menu">
                <header class="module-container">
                    <div class="header-part brand-logo">
                        <a href="/" title="Dacia">
                            <img src="https://unpkg.com/@wssto2/rv-dacia-header-footer-cg-bih-new-main/dist/img/dacia-logo.svg" alt="Dacia Logo" />
                        </a>
                    </div>

                    <nav class="header-part menu hidden-xs">
                    
                        <ul class="main-navigation">
                            <li class="visible-xs visible-sm">
                                <a class="home" href="/" title="Početna"> <span class="text-uppercase">Početna</span></a>
                            </li>
                            <Dropdown 
                                v-for="(navigationItem, itemIndex) in mainNavigation" 
                                :key="itemIndex" 
                                :item="navigationItem"
                                :itemIndex="itemIndex"
                                    @showModal="toggleCarCategory"
                                />
                            
                        </ul>

                    </nav>
                    <div class="header-part menu-trigger ico-before-menu" data-phf-ico-before="" @click="toggleMobileDropdown"></div>
                </header>
                <nav :class="['mobile-menu', { 'mobile-menu-activated' : mobileDropdown}]" :style="mobileDropdown ? 'display: block;' : 'display: none;'">
                    <div class="close-button menu-trigger" @click="toggleMobileDropdown" data-phf-ico-after=""></div>
        
                    <ul class="main-navigation">
                        <li class="visible-xs visible-sm">
                            <a class="header-icon header-icon-home text-uppercase" href="/"><span>Početna</span></a>
                        </li>
                        
                        <Dropdown 
                            v-for="(navigationItem, itemIndex) in mainNavigation" 
                            :key="itemIndex" 
                            :item="navigationItem"
                            :itemIndex="itemIndex"
                                @showModal="toggleCarCategory"
                            />
                        
                    </ul>
                    <div class="navigation-top-mobile">
                        <ul class="module-container navigation-top-mobile">
                            
                            <li v-for="(topMenuItem, topMenuIndex) in topNavigation" :key="topMenuIndex" class="text-uppercase link-left">
                                <a v-if="topMenuIndex != 0" :data-tracking-name="topNavIndexClass" data-tracking-location="Menu top navigation" class="gtm-button link-left_link" :data-phf-ico-before="topMenuItem.icon" :href="topMenuItem.url" title="" target="_self" rel="nofollow noreferrer">
                                    <span><span>{{ topMenuItem.title }}</span></span>
                                </a>
                            </li>
                            
                        </ul>
                    </div>
                </nav>
                <div class="nav-mask"></div>
            </div>
            <div :class="['menu-popup', { 'is-active' : (Number.isInteger(activeCarCategory)) }]" style="min-height: 938px;">
                <div class="menu-popup_close" data-phf-ico-before="" data-phf-ico-active-before=""></div>
                <div class="menu-popup_inner" v-if="mainNavFirst != 0">

                    <div v-show="activeCarCategory === carPickerIndex" v-for="(carPicker, carPickerIndex) in mainNavFirst" :key="carPickerIndex" class="menu-popup_vehicles-container  is-active" data-popup="595468798" style="left: 390px;">
                        
                        <a :href="carPicker.url" class="menu-popup_vehicle-block">
                            <span class="menu-popup_icon" :data-phf-ico-after="carPicker.icon"></span>
                            <span class="menu-popup_vehicle-name">{{carPicker.title}}</span>
                        </a>
                        <a  v-for="(carElement, carElementIndex) in carPicker.children" :key="carElementIndex" :href="carElement.url" target="_self" class="menu-popup_vehicle-block">
                            <span class="menu-popup_img">
                                <img :src="carElement.icon">
                            </span>
                            <span class="menu-popup_vehicle-name">{{carElement.title}}</span>
                        </a>
                    </div>                                
                    
                </div>
            </div>
            <div class="menu-popup_mask"></div>
        </div>

        <nav class="progress-nav helios-special-elements"></nav>
    </div>
</div>

</template>

<script>
    import axios from 'axios';
    import Dropdown from "@/components/Dropdown";

    export default {
        components: { Dropdown },
        name: 'Header',
        created() {
            if ("HEADER_FOOTER_SETTINGS" in window) {
                this.fetchNavigation(window.HEADER_FOOTER_SETTINGS.apiUri);
            } else {
                if (this.url) {
                    this.fetchNavigation(this.url);
                }
            }

            window.addEventListener('click', this.del)
        },
        data() {
            return {
                image: require('@/assets/images/dacia-logo.svg'),
                expanded: false,
                mobileDropdown: false,
                activeCarCategory: null,
                topNavigation: [],                 
                mainNavigation: []
            }
        },
        beforeDestroy() {
            window.removeEventListener('click', this.del)
        },

        computed: {
            topNavIndexClass(){
                return true
            },
            
            mainNavFirst() {
                return  this.mainNavigation && this.mainNavigation[0] && this.mainNavigation[0].children;
            }
        },
        methods: {
            fetchNavigation(apiUri) {
                axios.get(apiUri)
                    .then((response) => {
                        this.topNavigation = response.data.top_navigation.schema;
                        this.mainNavigation = response.data.main_navigation.schema;
                    })
            },
            toggleMobileDropdown(){
                this.mobileDropdown = !this.mobileDropdown
            },
            toggleCarCategory(index){
                console.log(index);
                if(this.activeCarCategory === index) {
                    this.activeCarCategory = null
                } else {
                    this.activeCarCategory = index
                }
                if(index === null) {
                    this.activeCarCategory = null
                }
                
            },
            del(e) {
                if(! this.$el.contains(e.target)){
                    this.mobileDropdown = false
                }
            }
        }
    }
</script>

<style scoped>
    @import './assets/css/main.css';

    @font-face{
        font-family: Read-Bold;
        font-weight:normal;
        font-display:swap;
        src:url(./assets/fonts/Read-Bold_V3000.woff2) format("woff2")
    }
    @font-face{
        font-family: Read-Regular;
        font-weight:normal;
        font-display:swap;
        src:url(./assets/fonts/Read-Regular_V3000.woff2) format("woff2")
    }
    @font-face{
        font-family: DaciaBlock-Bold;
        font-weight:normal;
        font-display:swap;
        src:url(./assets/fonts/DaciaBlock-Bold.woff2) format("woff2")
    }
    @font-face{
        font-family:Pictos;
        font-style: normal;
        font-weight: 400;
        src:url(./assets/fonts/PictosComplete-Regular.woff) format("woff")
    }
</style>
