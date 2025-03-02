<script setup>
import { ref } from "vue";
const localePath = useLocalePath()
import {
  Dialog,
  DialogPanel,
  Disclosure,
  DisclosureButton,
  DisclosurePanel,
  Popover,
  PopoverButton,
  PopoverGroup,
  PopoverPanel,
  Menu,
  MenuButton,
  MenuItem,
  MenuItems,
} from "@headlessui/vue";
import { SafeIcon } from "@bitcoin-design/bitcoin-icons-vue/outline";
import {
  Bars3Icon,
  XMarkIcon,
  MapIcon,
  ShoppingCartIcon,
  ChevronDownIcon,
} from "@heroicons/vue/24/outline";
const mobileMenuOpen = ref(false);
import data from "~/config/setup";

import menu from "~/config/menu";


const links = data.socialnavigation;
// import { useProjectStore } from "~/store/shopcart";

// const store = useProjectStore();
// const totalItems = ref(store.getTotalItems());

// Watch for changes in the cartItems array
// watch(
//   () => store.cartItems,
//   () => {
//     totalItems.value = store.getTotalItems();
//   },
//   { deep: true }
// );

// onMounted(() => {
//   totalItems.value = store.getTotalItems();
// });
</script>

<template>
  <header
    class="bg-colorBgDark backdrop-blur-sm fixed w-full z-50 shadow-2xl shadow-gray-700"
  >
    <nav
      class="mx-auto flex max-w-7xl items-center justify-between p-2 lg:px-8"
      aria-label="Global"
    >
      <div class="flex lg:flex-1">
        <NuxtLink :to="localePath('/')" class="flex">
          <span class="sr-only">{{ data.name }}</span>



          <span
            v-if="!data.logo"
            class="mt-1.5 ml-4 text-2xl text-purple-500"
            >{{ data.textlogo }}</span
          >

        </NuxtLink>
      </div>

      <div class="flex lg:hidden">


        <div
          class="flex lg:hidden border-2 rounded-md border-white p-1"
        >


        
          <button
            type="button"
            class="-m-2.5 inline-flex items-center justify-center rounded-md p-2.5 text-white"
            @click="mobileMenuOpen = true"
          >
            <span class="sr-only">Open main menu</span>
            <Bars3Icon
              class="h-6 w-6 text-white"
              aria-hidden="true"
            />
          </button>
        </div>
      </div>
      <PopoverGroup class="hidden lg:flex lg:gap-x-12">
        <NuxtLink
          :to="localePath(item.href)"
          v-for="item in menu.Headernavigation.basicmenu"
          :key="item.name"
          class="text-sm font-semibold leading-6 text-white"
        >
          {{ item.name }}</NuxtLink
        >






        <Popover class="relative"  v-if="menu.Headernavigation.MenuPopupName">
          <PopoverButton
            class="flex items-center gap-x-1 text-sm font-semibold leading-6 text-white"
          >
            {{ menu.Headernavigation.MenuPopupName }}
            <ChevronDownIcon
              class="h-5 w-5 flex-none text-white"
              aria-hidden="true"
            />
          </PopoverButton>

          <transition
            enter-active-class="transition ease-out duration-200"
            enter-from-class="opacity-0 translate-y-1"
            enter-to-class="opacity-100 translate-y-0"
            leave-active-class="transition ease-in duration-150"
            leave-from-class="opacity-100 translate-y-0"
            leave-to-class="opacity-0 translate-y-1"
          >
            <PopoverPanel
              class="absolute -left-8 top-full z-10 mt-3 w-screen max-w-md overflow-hidden rounded-3xl bg-gray-200 shadow-lg ring-1 ring-gray-900/5"
            >
              <div class="p-4">
                <div
                  v-for="item in menu.Headernavigation.MenuPopup"
                  :key="item.name"
                  class="group relative flex gap-x-6 rounded-lg p-4 text-sm leading-6 hover:bg-gray-50"
                >
                  <div class="flex-auto">
                    <NuxtLink
                      :to="localePath(item.href)"
                      class="block font-semibold text-gray-900"
                    >
                      {{ item.name }}
                      <span class="absolute inset-0" />
                    </NuxtLink>
                    <p class="mt-1 text-gray-600">{{ item.description }}</p>
                  </div>
                </div>
              </div>
              <div
                class="grid grid-cols-2 divide-x divide-gray-900/5 bg-gray-50"
              >
                <NuxtLink
                  v-for="item in callsToAction"
                  :key="item.name"
                  :to="localePath(item.href)"
                  class="flex items-center justify-center gap-x-2.5 p-3 text-sm font-semibold leading-6 text-gray-900 hover:bg-gray-100"
                >
                  <component
                    :is="item.icon"
                    class="h-5 w-5 flex-none text-gray-400"
                    aria-hidden="true"
                  />
                  {{ item.name }}
                </NuxtLink>
              </div>
            </PopoverPanel>
          </transition>
        </Popover>


      </PopoverGroup>
      <div class="hidden lg:flex lg:flex-1 lg:justify-end">




        <SettingsLanguage v-if="data.multilang" />





      </div>
    </nav>
    <Dialog
      as="div"
      class="lg:hidden"
      @close="mobileMenuOpen = false"
      :open="mobileMenuOpen"
    >
      <div class="fixed inset-0 z-10" />

      <DialogPanel
        class="fixed inset-y-0 right-0 z-10 w-full overflow-y-auto bg-colorBgLight dark:bg-colorBgDark p-3 lg:px-8 sm:max-w-sm sm:ring-1 sm:ring-gray-900/10"
      >
        <div class="flex items-center justify-between">

          <!--
          <NuxtLink to="/" class="-m-1.5 p-1.5">
            <img
              class="h-12 w-auto hidden dark:block"
              :src="'/project/' + data.logodarkimage"
              alt=""
            />

            <img
              class="h-12 w-auto dark:hidden block"
              :src="'/project/' + data.logoimage"
              alt=""
            />
          </NuxtLink>

 -->

          
          <button
            type="button"
            class="-m-2.5 rounded-md p-2.5 text-gray-700 text-white "
            @click="mobileMenuOpen = false"
          >
            <span class="sr-only">Close menu</span>
            <XMarkIcon class="h-6 w-6" aria-hidden="true" />
          </button>
        </div>
        <div class="mt-6 flow-root px-3">
          <div class="-my-6 divide-y divide-white ">
            <div class="space-y-2 py-6">
              <NuxtLink
                :to="localePath(item.href)"
                v-for="item in menu.Headernavigation.basicmenu"
                :key="item.name"
                @click="mobileMenuOpen = false"
                class="-mx-3 block rounded-lg px-3 py-2 text-base font-semibold leading-7 text-white"
              >
                {{ item.name }}</NuxtLink
              >
              <div v-if="menu.Headernavigation.MenuPopupName">
              <NuxtLink
                :to="localePath(item.href)"
                
                v-for="item in menu.Headernavigation.MenuPopup"
                @click="mobileMenuOpen = false"
                :key="item.name"
                class="-mx-3 block rounded-lg px-3 py-2 text-base font-semibold leading-7 text-white"
              >
                {{ item.name }}</NuxtLink
              ></div>

        <!-- <NuxtLink
                v-if="data.blog"
                class="-mx-3 block rounded-lg px-3 py-2 text-base font-semibold leading-7 text-black dark:text-white"
                :to="localePath('/blog')"
                @click="mobileMenuOpen = false"
              >
              {{menu.Headernavigation.bloglabel}}
              </NuxtLink>

              <NuxtLink
                v-if="data.shop"
                class="-mx-3 block rounded-lg px-3 py-2 text-base font-semibold leading-7 text-black dark:text-white"
                :to="localePath('/shop')"
                @click="mobileMenuOpen = false"
              >
              {{menu.Headernavigation.shoplabel}}
              </NuxtLink>



              <a
              class="-mx-3 block rounded-lg px-3 py-2 text-base font-semibold leading-7 text-black dark:text-white"
              href="https://publish.obsidian.md/cypher/"
          target="_blank"
        >
        Docs
      </a> -->


<!-- 
              <NuxtLink
                v-if="data.contact"
                class="-mx-3 block rounded-lg px-3 py-2 text-base font-semibold leading-7 text-black dark:text-white"
                :to="localePath('/contact')"
                @click="mobileMenuOpen = false"
              >
                Contact
              </NuxtLink> -->



            </div>

            <div class="py-6">
              <!-- <SettingsSocials class="mx-auto pt-3" /> -->

              <div class="flex flex-row justify-center my-3 mt-6">
                <!-- <SettingsTheme /> -->

                <SettingsLanguage v-if="data.multilang" />
              </div>
            </div>
          </div>
        </div>
      </DialogPanel>
    </Dialog>
  </header>
</template>
