<template>

    <li :class="[{'expand-box' : !item.url},{'no-expand' : item.url}]">
        <a @click="toggleDropdown" :href="item.url ? item.url : '#'" :class="['dropdown', 'text-uppercase', {'active' : dropdownActive}]" :data-phf-ico-after="item.url ? '' : ''" data-phf-ico-active-after="" :title="item.title" target="_self">
            <span>{{item.title}}</span>
        </a>
        
        <div v-if="itemIndex === 0 && item != 0" :class="['expand-container', {'active' : dropdownActive}]">
            <div class="expand grid-row">
                <div class="sub-nav sub-nav--expandable ">
                    <ul>

                        <li v-for="(carCategory, carIndex) in item.children" :key="carIndex" class="sub-nav_item">
                             <a @click="toggleCarCategory(carIndex)" class="sub-nav_link-expandable" data-phf-ico-active-after="" data-phf-ico-after="" href="#" target="_self" :title="carCategory.title"><span>{{carCategory.title}}</span></a>
                        </li>
                        
                    </ul>
                </div>
            </div>
        </div>
                                        
        <div v-if="itemIndex !== 0 && !item.url && item != 0" :class="['expand-container', {'active' : dropdownActive}]">
            <div class="expand grid-row">
                <div class="sub-nav  ">
                    <ul>
                        <li v-for="(dropdownLink, dropdownLinkIndex) in item.children" :key="dropdownLinkIndex">
                            <a class="sub-nav_link" :href="dropdownLink.url" target="_self" :title="dropdownLink.title"><span>{{dropdownLink.title}}</span></a>
                        </li>                        
                    </ul>
                </div>
            </div>
        </div>
        
    </li>
</template>

<script>

    export default {
        name: "Dropdown",

        props: {
            item: Object,
            itemIndex: Number
        },

        data(){
            return {
                dropdownActive: false
            }
        },
        created() {
            window.addEventListener('click', this.close)
        },
        beforeDestroy(){
            window.removeEventListener('click', this.close)
        },
        methods: {
            toggleCarCategory(index){
                this.$emit("showModal", index)
            },
            closeDropdown(){
                this.dropdownActive = false;
            },
            toggleDropdown(){
                this.dropdownActive = !this.dropdownActive
                this.$emit("showModal", null)
            },
            close(e) {
                if(! this.$el.contains(e.target)){
                    if(this.dropdownActive){
                        this.$emit("showModal", null)
                    }
                    this.dropdownActive = false;
                }
            }
        }

    }
</script>

<style>

</style>