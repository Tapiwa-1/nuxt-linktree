<template>
    <!-- :id="`LinkBox${link.id}`" -->
    <div id="LinkBox"   class="w-full bg-white rounded-3xl">
        <div id="MainLinkBoxSection" class="px-8 py-5">
            <div class="flex items-center justify-between py-1">
                <div class="flex items-center w-full">
                    <!-- v-if="editName(selectedId, selectedStr)" -->
                    <!-- :id="`editNameInput-${link.id}`" -->
                   
                </div>
                <div class="flex items-center">
                    <Icon 
                        class="cursor-pointer"
                        :class="isMobile ? 'min-w-[23px]' : 'min-w-[17px]'"
                        name="mdi:bell-outline" 
                        :size="isMobile ? '25' : '20'"
                        color="#676B5F"
                    />
                </div>
            </div>
            <div class="flex items-center justify-between py-1">
                <div class="flex items-center w-full">
                    <input 
                        v-if="true"
                        id="editLinkInput"
                        type="text"
                        v-model="url"
                        class="w-full text-sm  focus:outline-none"
                    >
                    <div v-else class="flex items-center w-[calc(100%-2px)]">
                    
                        <div 
                            class="mr-2 truncate cursor-pointer"
                            :class="isMobile ? 'text-xl' : 'text-sm'"
                        >
                            TESTING URL
                        </div>
                      
                        <Icon 
                            class="cursor-pointer min-w-[17px] "
                            :class="isMobile ? 'min-w-[23px]' : 'min-w-[17px]'"
                            name="octicon:pencil-24" 
                            :size="isMobile ? '23' : '17'" 
                            color="#676B5F"
                        />
                    </div>
                </div>
            </div>
            <div class="flex items-center justify-between py-1 mt-4">
                <div class="flex items-center w-full relative">
                    <div 
                        class="flex items-center px-1.5 py-[2px] absolute -left-[6px] rounded-md"
                        :class="isUploadImage ? 'bg-[#8228D9]' : 'hover:bg-gray-200'"
                    >
                        <Icon 
                            @click="editImage()"
                            class="cursor-pointer"
                            name="icon-park-twotone:collect-picture" 
                            :size="isMobile ? '23' : '17'" 
                            :color="isUploadImage ? '#FFFFFF' : '#676B5F'"
                        />
                    </div>
                </div>
                <div v-if="!isMobile" class="flex items-center">
                    <div class="flex items-center px-1 5 py-[2px] rounded-md relative">
                        <button 
                            @click="isDelete = true; isUploadImage = false"
                            class="flex items-center px-1.5 py-[2px] absolute -right-[6px] rounded-md"
                            :class="isDelete ? 'bg-[#8228D9]' : 'hover:bg-gray-200'"
                        >
                            <Icon 
                                class="cursor-pointer"
                                name="carbon:trash-can" 
                                size="20" 
                                :color="isDelete ? '#FFFFFF' : '#676B5F'"
                            />
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <div
            id="FooterLinkDeleteSection"
            class="overflow-hidden"
            :class="[
                {'max-h-[180px] transition-all duration-300 ease-in': isDelete},
                {'max-h-0 transition-all duration-200 ease-out': !isDelete},
            ]"
        >
            <button 
                @click="isDelete = false"
                class="relative w-full bg-[#DFE2D9] py-1.5"
            >
                <Icon
                    name="mdi:close" 
                    class="absolute right-1 top-[6px] cursor-pointer" 
                    size="20" 
                    color="#45494A"
                />
                <div class="text-center text-sm font-bold text-[#45494A]">
                    Delete
                </div>
            </button>
            <div class="flex items-center justify-center pt-3">
                Delete this forever?
            </div>
            <div class="w-full px-4 py-3">

                <div class="flex items-center gap-2 w-full">
                    <button     
                        @click="deleteLink()"
                        class="
                            flex 
                            items-center 
                            border 
                            justify-center 
                            w-full 
                            py-3 
                            rounded-full 
                            text-black 
                            font-semibold 
                            hover:bg-gray-100
                        "
                    >
                        Remove
                    </button>
                </div>
            </div>
        </div>
        <div
            id="FooterLinkBoxSection"
            class="overflow-hidden"
            :class="[
                {'max-h-[180px] transition-all duration-300 ease-in': isUploadImage},
                {'max-h-0 transition-all duration-200 ease-out': !isUploadImage},
            ]"
        >
            <div class="relative w-full bg-[#DFE2D9] py-1.5">
                <Icon 
                    @click="isUploadImage = false"
                    name="mdi:close" 
                    class="absolute right-1 top-[6px] cursor-pointer" 
                    size="20" 
                    color="#45494A"
                />
                <div class="text-center text-sm font-bold text-[#45494A]">
                    Add Thumbnail
                </div>
            </div>
            <div class="w-full flex items-center justify-between px-4 py-5">
                <img
                    src="https://picsum.photos/200"
                    class="rounded-lg w-[100] aspect-quare"
                >
                <div class="w-full pl-3">
                    
                <div class="w-full pl-3">
                    <button 
                        @click="openCropper = true"
                        class="
                            flex 
                            items-center 
                            justify-center 
                            w-full 
                            py-3 
                            rounded-full 
                            text-white 
                            font-semibold 
                            bg-[#8228D9] 
                            hover:bg-[#6c21b3] 
                            mb-2
                        "
                    >
                        Change
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { useUserStore } from '~~/stores/user';
import { storeToRefs } from 'pinia';
const userStore = useUserStore()
const { isMobile, updatedLinkId } = storeToRefs(userStore)

const props = defineProps({
    link: Object,
    selectedId: { type: Number, default: 0 },
    selectedStr: { type: String, default: '' },
})

const { link, selectedId, selectedStr } = toRefs(props)

const emit = defineEmits(['updatedInput'])

let name = ref('')
let url = ref('')
let data = ref(null)
let isDelete = ref(false)
let openCropper = ref(false)
let isUploadImage = ref(false)

onMounted (() =>{
    name.value = link.value.name
    url.value = link.value.url

    document.addEventListener('mouseup', function (e){
        let editNameInput = document.getElementById(`editNameInput-${link.value.id}`)
        if (
            editNameInput &&
            !editNameInput.contains(e.target) && 
            selectedStr.value == 'isName' && 
            link.value.id == selectedId.value
        ) { 
            editNameInput.blur() 
            emit('updatedInput', { id: 0, str: '' })
        }
    })
})
</script>