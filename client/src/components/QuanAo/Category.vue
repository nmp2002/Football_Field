<template>
    <div class="wrapper">
      <div class="bg-white">
        <div class="mx-auto max-w-2xl px-4 py-16 sm:px-6 sm:py-24 lg:max-w-7xl lg:px-8">
          <h2 class="text-2xl font-bold tracking-tight text-gray-900">Quần Áo Đá</h2>
    
          <div class="mt-6 grid grid-cols-1 gap-x-6 gap-y-10 sm:grid-cols-2 lg:grid-cols-4 xl:gap-x-8">
            <div v-for="product in currentPageProducts" :key="product.id" class="group relative">
              <div
                class="aspect-h-1 aspect-w-1 w-full overflow-hidden rounded-md bg-gray-200 lg:aspect-none group-hover:opacity-75 lg:h-80">
                <img :src="product.imageSrc" :alt="product.imageAlt"
                  class="h-full w-full object-cover object-center lg:h-full lg:w-full" />
              </div>
              <div class="mt-4 flex justify-between">
                <div>
                  <h3 class="text-sm text-gray-700">
                    <a :href="product.href" @click="openProductDialog(product)">
                      <span aria-hidden="true" class="absolute inset-0"></span>
                      {{ product.name }}
                    </a>
                  </h3>
                  <p class="mt-1 text-sm text-gray-500">{{ product.color }}</p>
                </div>
                <p class="text-sm font-medium text-gray-900">{{ product.price }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    
      <TransitionRoot as="template" :show="dialogOpen">
        <Dialog as="div" class="relative z-10" @close="closeProductDialog">
          <TransitionChild as="template" enter="ease-out duration-300" enter-from="opacity-0" enter-to="opacity-100"
            leave="ease-in duration-200" leave-from="opacity-100" leave-to="opacity-0">
            <div class="fixed inset-0 hidden bg-gray-500 bg-opacity-75 transition-opacity md:block"></div>
          </TransitionChild>
    
          <div class="fixed inset-0 z-10 w-screen overflow-y-auto">
            <div class="flex min-h-full items-stretch justify-center text-center md:items-center md:px-2 lg:px-4">
              <TransitionChild as="template" enter="ease-out duration-300"
                enter-from="opacity-0 translate-y-4 md:translate-y-0 md:scale-95"
                enter-to="opacity-100 translate-y-0 md:scale-100" leave="ease-in duration-200"
                leave-from="opacity-100 translate-y-0 md:scale-100"
                leave-to="opacity-0 translate-y-4 md:translate-y-0 md:scale-95">
                <DialogPanel
                  class="flex w-full transform text-left text-base transition md:my-8 md:max-w-2xl md:px-4 lg:max-w-4xl">
                  <div
                    class="content relative flex w-full items-center overflow-hidden bg-white px-4 pb-8 pt-14 shadow-2xl sm:px-6 sm:pt-8 md:p-6 lg:p-8">
                    <button type="button"
                      class="absolute right-4 top-4 text-gray-400 hover:text-gray-500 sm:right-6 sm:top-8 md:right-6 md:top-6 lg:right-8 lg:top-8"
                      @click="closeProductDialog">
                      <span class="sr-only">Close</span>
                      <XMarkIcon class="h-6 w-6" aria-hidden="true" />
                    </button>
    
                    <div class="grid w-full grid-cols-1 items-start gap-x-6 gap-y-8 sm:grid-cols-12 lg:gap-x-8">
                      <div class="aspect-h-3 aspect-w-2 overflow-hidden rounded-lg bg-gray-100 sm:col-span-4 lg:col-span-5">
                        <img :src="selectedProduct.imageSrc" :alt="selectedProduct.imageAlt"
                          class="object-cover object-center" />
                      </div>
                      <div class="sm:col-span-8 lg:col-span-7">
                        <h2 class="text-2xl font-bold text-gray-900 sm:pr-12">{{ selectedProduct.name }}</h2>
    
                        <section aria-labelledby="information-heading" class="mt-2">
                          <h3 id="information-heading" class="sr-only">Product information</h3>
    
                          <p class="text-2xl text-gray-900">{{ selectedProduct.price }}</p>
    
                          <!-- Reviews -->
                          <div class="mt-8 sm:hidden">
                            <h4 class="text-sm font-medium text-gray-900">Reviews</h4>
                            <div class="flex items-center">
                              <div class="flex items-center">
                                <StarIcon v-for="rating in [0, 1, 2, 3, 4]" :key="rating"
                                  :class="[selectedProduct.rating > rating ? 'text-gray-900' : 'text-gray-200', 'h-5 w-5 flex-shrink-0']"
                                  aria-hidden="true" />
                              </div>
                              <p class="sr-only">{{ selectedProduct.rating }} out of 5 stars</p>
                              <a href="#" class="ml-3 text-sm font-medium text-indigo-600 hover:text-indigo-500">{{
                                selectedProduct.reviewCount }} Đánh Giá</a>
                            </div>
                          </div>
                        </section>
    
                        <section aria-labelledby="options-heading" class="mt-10">
                          <h3 id="options-heading" class="sr-only">Product options</h3>
    
                          <form>
                            <!-- Colors -->
                            <div>
                              <h4 class="text-sm font-medium text-gray-900">Chọn Màu</h4>
    
                              <RadioGroup v-model="selectedColor" class="mt-4">
                                <RadioGroupLabel class="sr-only">Choose a color</RadioGroupLabel>
                                <span class="flex items-center space-x-3">
                                  <RadioGroupOption as="template" v-for="color in selectedProduct.colors" :key="color.name"
                                    :value="color" v-slot="{ active, checked }">
                                    <div
                                      :class="[color.selectedClass, active && checked ? 'ring ring-offset-1' : '', !active && checked ? 'ring-2' : '', 'relative -m-0.5 flex cursor-pointer items-center justify-center rounded-full p-0.5 focus:outline-none']">
                                      <RadioGroupLabel as="span" class="sr-only">{{ color.name }}</RadioGroupLabel>
                                      <span aria-hidden="true"
                                        :class="[color.class, 'h-8 w-8 rounded-full border border-black border-opacity-10']" />
                                    </div>
                                  </RadioGroupOption>
                                </span>
                              </RadioGroup>
                            </div>
    
                            <!-- Sizes -->
                            <div class="mt-10">
                              <div class="flex items-center justify-between">
                                <h4 class="text-sm font-medium text-gray-900">Size</h4>
                                <a href="#" class="text-sm font-medium text-indigo-600 hover:text-indigo-500">Size guide</a>
                              </div>
    
                              <RadioGroup v-model="selectedSize" class="mt-4">
                                <RadioGroupLabel class="sr-only">Choose a size</RadioGroupLabel>
                                <div class="grid grid-cols-4 gap-4">
                                  <RadioGroupOption as="template" v-for="size in selectedProduct.sizes" :key="size.name"
                                    :value="size" :disabled="!size.inStock" v-slot="{ active, checked }">
                                    <div
                                      :class="[size.inStock ? 'cursor-pointer bg-white text-gray-900 shadow-sm' : 'cursor-not-allowed bg-gray-50 text-gray-200', active ? 'ring-2 ring-indigo-500' : '', 'group relative flex items-center justify-center rounded-md border py-3 px-4 text-sm font-medium uppercase hover:bg-gray-50 focus:outline-none sm:flex-1']">
                                      <RadioGroupLabel as="span">{{ size.name }}</RadioGroupLabel>
                                      <span v-if="size.inStock"
                                        :class="[active ? 'border' : 'border-2', checked ? 'border-indigo-500' : 'border-transparent', 'pointer-events-none absolute -inset-px rounded-md']"
                                        aria-hidden="true"></span>
                                      <span v-else aria-hidden="true"
                                        class="pointer-events-none absolute -inset-px rounded-md border-2 border-gray-200">
                                        <svg class="absolute inset-0 h-full w-full stroke-2 text-gray-200"
                                          viewBox="0 0 100 100" preserveAspectRatio="none" stroke="currentColor">
                                          <line x1="0" y1="100" x2="100" y2="0" vector-effect="non-scaling-stroke" />
                                        </svg>
                                      </span>
                                    </div>
                                  </RadioGroupOption>
                                </div>
                              </RadioGroup>
                            </div>
    
                            <button type="submit"
                              class="mt-6 flex w-full items-center justify-center rounded-md border border-transparent bg-indigo-600 px-8 py-3 text-base font-medium text-white hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2">Thêm
                              Vào Giỏ Hàng</button>
                          </form>
                        </section>
                      </div>
                    </div>
                  </div>
                </DialogPanel>
              </TransitionChild>
            </div>
          </div>
        </Dialog>
      </TransitionRoot>
      <div class="pagination flex justify-center items-center p-4 lg:p-8">
        <button @click="prevPage" :disabled="page === 1" class="icon-pagination">
          <font-awesome-icon :icon="['fas', 'arrow-left']" />
        </button>
        <span>Trang {{ page }}</span>
        <button @click="nextPage" :disabled="page >= totalPages" class="icon-pagination">
          <font-awesome-icon :icon="['fas', 'arrow-right']" />
        </button>
      </div>
    </div>
  </template>
  
  
  <script setup>
  import { ref, onMounted, computed } from 'vue'
  import {
    Dialog,
    DialogPanel,
    RadioGroup,
    RadioGroupLabel,
    RadioGroupOption,
    TransitionChild,
    TransitionRoot,
  } from '@headlessui/vue'
  import { XMarkIcon } from '@heroicons/vue/24/outline'
  import { StarIcon } from '@heroicons/vue/20/solid'
  
  const page = ref(1);
  const pageSize = 4;
  const filteredProducts = ref([]);
  const products = [
    {
      id: 1,
      name: 'Áo Đá Việt Nam',
      href: '#',
      imageAlt: "Front of men's Basic Tee in black.",
      price: '300.000 Vnd',
      color: 'Black',
      rating: 3.9,
      reviewCount: 117,
      href: '#',
      imageSrc: 'https://th.bing.com/th/id/R.50c7934bd6541abbb51e48f4d9a3f87e?rik=t%2bUklWGK5pLWJA&riu=http%3a%2f%2fbelo.vn%2fwp-content%2fuploads%2f2018%2f11%2fz1188384674228_416a3f7ef7a51b72f6098f6a68536743.jpg&ehk=c%2fSM4moCTUr04VPvYN%2bT6incwFEJHXADwZ6kJmDKyHA%3d&risl=&pid=ImgRaw&r=0',
      imageAlt: 'Two each of gray, white, and black shirts arranged on table.',
      colors: [
        { name: 'White', class: 'bg-white', selectedClass: 'ring-gray-400' },
        { name: 'Gray', class: 'bg-gray-200', selectedClass: 'ring-gray-400' },
        { name: 'Black', class: 'bg-gray-900', selectedClass: 'ring-gray-900' },
      ],
      sizes: [
        { name: 'XXS', inStock: true },
        { name: 'XS', inStock: true },
        { name: 'S', inStock: true },
        { name: 'M', inStock: true },
        { name: 'L', inStock: true },
        { name: 'XL', inStock: true },
        { name: 'XXL', inStock: true },
        { name: 'XXXL', inStock: false },
      ],
    },
    {
      id: 2,
      name: 'Áo Đá Việt Nam',
      href: '#',
      price: '300.000 Vnd',
      color: 'Black',
      rating: 3.9,
      reviewCount: 117,
      href: '#',
      imageSrc: 'https://th.bing.com/th/id/R.50c7934bd6541abbb51e48f4d9a3f87e?rik=t%2bUklWGK5pLWJA&riu=http%3a%2f%2fbelo.vn%2fwp-content%2fuploads%2f2018%2f11%2fz1188384674228_416a3f7ef7a51b72f6098f6a68536743.jpg&ehk=c%2fSM4moCTUr04VPvYN%2bT6incwFEJHXADwZ6kJmDKyHA%3d&risl=&pid=ImgRaw&r=0',
      imageAlt: 'Two each of gray, white, and black shirts arranged on table.',
      colors: [
        { name: 'White', class: 'bg-white', selectedClass: 'ring-gray-400' },
        { name: 'Gray', class: 'bg-gray-200', selectedClass: 'ring-gray-400' },
        { name: 'Black', class: 'bg-gray-900', selectedClass: 'ring-gray-900' },
      ],
      sizes: [
        { name: 'XXS', inStock: true },
        { name: 'XS', inStock: true },
        { name: 'S', inStock: true },
        { name: 'M', inStock: true },
        { name: 'L', inStock: true },
        { name: 'XL', inStock: true },
        { name: 'XXL', inStock: true },
        { name: 'XXXL', inStock: false },
      ],
    }, {
      id: 3,
      name: 'Áo Đá Việt Nam',
      href: '#',
      price: '300.000 Vnd',
      color: 'Black',
      rating: 3.9,
      reviewCount: 117,
      href: '#',
      imageSrc: 'https://th.bing.com/th/id/R.50c7934bd6541abbb51e48f4d9a3f87e?rik=t%2bUklWGK5pLWJA&riu=http%3a%2f%2fbelo.vn%2fwp-content%2fuploads%2f2018%2f11%2fz1188384674228_416a3f7ef7a51b72f6098f6a68536743.jpg&ehk=c%2fSM4moCTUr04VPvYN%2bT6incwFEJHXADwZ6kJmDKyHA%3d&risl=&pid=ImgRaw&r=0',
      imageAlt: 'Two each of gray, white, and black shirts arranged on table.',
      colors: [
        { name: 'White', class: 'bg-white', selectedClass: 'ring-gray-400' },
        { name: 'Gray', class: 'bg-gray-200', selectedClass: 'ring-gray-400' },
        { name: 'Black', class: 'bg-gray-900', selectedClass: 'ring-gray-900' },
      ],
      sizes: [
        { name: 'XXS', inStock: true },
        { name: 'XS', inStock: true },
        { name: 'S', inStock: true },
        { name: 'M', inStock: true },
        { name: 'L', inStock: true },
        { name: 'XL', inStock: true },
        { name: 'XXL', inStock: true },
        { name: 'XXXL', inStock: false },
      ],
    }, {
      id: 4,
      name: 'Áo Đá Việt Nam',
      href: '#',
      price: '300.000 Vnd',
      color: 'Black',
      rating: 3.9,
      reviewCount: 117,
      href: '#',
      imageSrc: 'https://th.bing.com/th/id/R.50c7934bd6541abbb51e48f4d9a3f87e?rik=t%2bUklWGK5pLWJA&riu=http%3a%2f%2fbelo.vn%2fwp-content%2fuploads%2f2018%2f11%2fz1188384674228_416a3f7ef7a51b72f6098f6a68536743.jpg&ehk=c%2fSM4moCTUr04VPvYN%2bT6incwFEJHXADwZ6kJmDKyHA%3d&risl=&pid=ImgRaw&r=0',
      imageAlt: 'Two each of gray, white, and black shirts arranged on table.',
      colors: [
        { name: 'White', class: 'bg-white', selectedClass: 'ring-gray-400' },
        { name: 'Gray', class: 'bg-gray-200', selectedClass: 'ring-gray-400' },
        { name: 'Black', class: 'bg-gray-900', selectedClass: 'ring-gray-900' },
      ],
      sizes: [
        { name: 'XXS', inStock: true },
        { name: 'XS', inStock: true },
        { name: 'S', inStock: true },
        { name: 'M', inStock: true },
        { name: 'L', inStock: true },
        { name: 'XL', inStock: true },
        { name: 'XXL', inStock: true },
        { name: 'XXXL', inStock: false },
      ],
    }, {
      id: 5,
      name: 'Áo Đá Việt Nam',
      href: '#',
      price: '300.000 Vnd',
      color: 'Black',
      rating: 3.9,
      reviewCount: 117,
      href: '#',
      imageSrc: 'https://belo.vn/wp-content/uploads/2018/11/z1188384711253_b6f0da3ac39aa5537f3536ff9e1d82b8.jpg',
      imageAlt: 'Two each of gray, white, and black shirts arranged on table.',
      colors: [
        { name: 'White', class: 'bg-white', selectedClass: 'ring-gray-400' },
        { name: 'Gray', class: 'bg-gray-200', selectedClass: 'ring-gray-400' },
        { name: 'Black', class: 'bg-gray-900', selectedClass: 'ring-gray-900' },
      ],
      sizes: [
        { name: 'XXS', inStock: true },
        { name: 'XS', inStock: true },
        { name: 'S', inStock: true },
        { name: 'M', inStock: true },
        { name: 'L', inStock: true },
        { name: 'XL', inStock: true },
        { name: 'XXL', inStock: true },
        { name: 'XXXL', inStock: false },
      ],
    }, {
      id: 6,
      name: 'Áo Đá Việt Nam',
      href: '#',
      price: '300.000 Vnd',
      color: 'Black',
      rating: 3.9,
      reviewCount: 117,
      href: '#',
      imageSrc: 'https://belo.vn/wp-content/uploads/2018/11/z1188384711253_b6f0da3ac39aa5537f3536ff9e1d82b8.jpg',
      imageAlt: 'Two each of gray, white, and black shirts arranged on table.',
      colors: [
        { name: 'White', class: 'bg-white', selectedClass: 'ring-gray-400' },
        { name: 'Gray', class: 'bg-gray-200', selectedClass: 'ring-gray-400' },
        { name: 'Black', class: 'bg-gray-900', selectedClass: 'ring-gray-900' },
      ],
      sizes: [
        { name: 'XXS', inStock: true },
        { name: 'XS', inStock: true },
        { name: 'S', inStock: true },
        { name: 'M', inStock: true },
        { name: 'L', inStock: true },
        { name: 'XL', inStock: true },
        { name: 'XXL', inStock: true },
        { name: 'XXXL', inStock: false },
      ],
    }, {
      id: 7,
      name: 'Áo Đá Việt Nam',
      href: '#',
      price: '300.000 Vnd',
      color: 'Black',
      rating: 3.9,
      reviewCount: 117,
      href: '#',
      imageSrc: 'https://belo.vn/wp-content/uploads/2018/11/z1188384711253_b6f0da3ac39aa5537f3536ff9e1d82b8.jpg',
      imageAlt: 'Two each of gray, white, and black shirts arranged on table.',
      colors: [
        { name: 'White', class: 'bg-white', selectedClass: 'ring-gray-400' },
        { name: 'Gray', class: 'bg-gray-200', selectedClass: 'ring-gray-400' },
        { name: 'Black', class: 'bg-gray-900', selectedClass: 'ring-gray-900' },
      ],
      sizes: [
        { name: 'XXS', inStock: true },
        { name: 'XS', inStock: true },
        { name: 'S', inStock: true },
        { name: 'M', inStock: true },
        { name: 'L', inStock: true },
        { name: 'XL', inStock: true },
        { name: 'XXL', inStock: true },
        { name: 'XXXL', inStock: false },
      ],
    }, {
      id: 8,
      name: 'Áo Đá Việt Nam',
      href: '#',
      price: '300.000 Vnd',
      color: 'Black',
      rating: 3.9,
      reviewCount: 117,
      href: '#',
      imageSrc: 'https://belo.vn/wp-content/uploads/2018/11/z1188384711253_b6f0da3ac39aa5537f3536ff9e1d82b8.jpg',
      imageAlt: 'Two each of gray, white, and black shirts arranged on table.',
      colors: [
        { name: 'White', class: 'bg-white', selectedClass: 'ring-gray-400' },
        { name: 'Gray', class: 'bg-gray-200', selectedClass: 'ring-gray-400' },
        { name: 'Black', class: 'bg-gray-900', selectedClass: 'ring-gray-900' },
      ],
      sizes: [
        { name: 'XXS', inStock: true },
        { name: 'XS', inStock: true },
        { name: 'S', inStock: true },
        { name: 'M', inStock: true },
        { name: 'L', inStock: true },
        { name: 'XL', inStock: true },
        { name: 'XXL', inStock: true },
        { name: 'XXXL', inStock: false },
      ],
    },
  
  ]
  
  const totalPages = computed(() => Math.ceil(products.length / pageSize));
  
  const prevPage = () => {
    if (page.value > 1) {
      page.value--;
    }
  };
  
  const nextPage = () => {
    if (page.value < totalPages.value) {
      page.value++;
    }
  };
  
  const currentPageProducts = computed(() => {
    const startIdx = (page.value - 1) * pageSize;
    const endIdx = startIdx + pageSize;
    return products.slice(startIdx, endIdx);
  });
  onMounted(() => {
    filteredProducts.value = products;
  });
  const openProductDialog = (product) => {
    selectedProduct.value = product;
    dialogOpen.value = true;
  }
  
  const closeProductDialog = () => {
    dialogOpen.value = false;
  }
  
  const selectedProduct = ref(null);
  const dialogOpen = ref(false);
  const selectedColor = ref(null);
  const selectedSize = ref(null);
  </script>
  <style scoped>
  .wrapper {
    margin: 10px;
  }
  
  .content {
    margin-top: 120px;
  }
  
  .pagination {
    padding-bottom: 100px;
  }
  
  .icon-pagination {
    padding: 0 10px;
  }
  
  .icon-pagination:hover {
    font-size: 17px;
    color: rgb(250, 69, 22);
  }
  </style>