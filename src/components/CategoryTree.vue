<template>
  <div>
    <div class="button-container">
        <button @click="exportToJson">Экспорт отчета в JSON</button>
        <button @click="exportToCsv">Экспорт отчета в CSV</button>
        <button @click="addSelectedToCart">Купить</button>
        <button @click="sortByName">Сортировать по алфавиту</button>
        <button @click="sortByPrice">Сортировать по цене</button>
      </div>
    <ul>
      <category-node :category="category" v-for="category in categories" :key="category.id" @select-product="toggleProductSelection" />
    </ul>
  </div>
</template>

<script>
import CategoryNode from './CategoryNode.vue';
import { saveAs } from 'file-saver';

export default {
  name: 'CategoryTree',
  components: {
    CategoryNode
  },
  data() {
    return {
      categories: [
        {
          id: 1,
          name: 'Электроника',
          products: [],
          categories: [
            {
              id: 1,
              name: 'Бытовая',
              products: [],
              categories: [
                {
                  id: 1,
                  name: 'Кухня',
                  products: [],
                  categories: [
                    {
                      id: 1,
                      name: 'Встраиваемая',
                      products: [],
                      categories: [
                        {
                          id: 1,
                          name: 'Посудомойки',
                          products: [
                            { id: 1, name: 'Bosch SMS24AW01R', price: 25000 },
                            { id: 2, name: 'Electrolux ESL95321LO', price: 27000 }
                          ],
                          categories: [
                            {
                              id: 1,
                              name: 'Bosh',
                              products: [
                                { id: 1, name: 'Bosch SMS24AW01R', price: 25000 },
                                { id: 2, name: 'Bosch SMV25EX01R', price: 30000 }
                              ],
                              categories: []
                            }
                          ]
                        }
                      ]
                    },
                    {
                      id: 2,
                      name: 'Не встраиваемая',
                      products: [
                        { id: 3, name: 'Indesit DFG 15B10', price: 22000 },
                        { id: 4, name: 'Candy CDP 2L952W', price: 20000 }
                      ],
                      categories: []
                    }
                  ]
                },
                {
                  id: 2,
                  name: 'Инструменты',
                  products: [
                    { id: 5, name: 'Дрель Bosch', price: 5000 },
                    { id: 6, name: 'Шуруповерт DeWalt', price: 8000 }
                  ],
                  categories: []
                },
                {
                  id: 3,
                  name: 'В машину',
                  products: [
                    { id: 7, name: 'Автомобильный пылесос Philips', price: 3000 },
                    { id: 8, name: 'Зарядное устройство Xiaomi', price: 1500 }
                  ],
                  categories: []
                }
              ]
            },
            {
              id: 2,
              name: 'Игровая',
              products: [],
              categories: [
                {
                  id: 1,
                  name: 'Игровые ноутбуки',
                  products: [
                    { id: 9, name: 'ASUS ROG Strix', price: 120000 },
                    { id: 10, name: 'MSI Gaming', price: 110000 }
                  ],
                  categories: []
                }
              ]
            }
          ]
        },
        {
          id: 2,
          name: 'Мебель',
          products: [],
          categories: [
            {
              id: 1,
              name: 'Гостиная',
              products: [],
              categories: [
                {
                  id: 1,
                  name: 'Диваны',
                  products: [
                    { id: 11, name: 'Угловой диван', price: 30000 },
                    { id: 12, name: 'Диван-кровать', price: 25000 }
                  ],
                  categories: []
                },
                {
                  id: 2,
                  name: 'Кресла',
                  products: [
                    { id: 13, name: 'Кресло реклайнер', price: 15000 },
                    { id: 14, name: 'Массажное кресло', price: 35000 }
                  ],
                  categories: []
                }
              ]
            },
            {
              id: 2,
              name: 'Спальня',
              products: [],
              categories: [
                {
                  id: 1,
                  name: 'Кровати',
                  products: [
                    { id: 15, name: 'Двуспальная кровать', price: 20000 },
                    { id: 16, name: 'Кровать с подъемным механизмом', price: 25000 }
                  ],
                  categories: []
                },
                {
                  id: 2,
                  name: 'Шкафы',
                  products: [
                    { id: 17, name: 'Шкаф-купе', price: 18000 },
                    { id: 18, name: 'Шкаф для одежды', price: 15000 }
                  ],
                  categories: []
                }
              ]
            }
          ]
        }
      ],
      selectedProducts: [],
      cart: [],
      isNameSortedAsc: true,
      isPriceSortedAsc: true
    };
  },
  methods: {
    toggleProductSelection(product) {
      const index = this.selectedProducts.findIndex(p => p.id === product.id);
      if (index !== -1) {
        this.selectedProducts.splice(index, 1);
      } else {
        this.selectedProducts.push(product);
      }
    },
    addSelectedToCart() {
      const currentDateTime = new Date().toLocaleString();
      this.selectedProducts.forEach(product => {
        this.cart.push({ ...product, purchaseDateTime: currentDateTime });
      });
      this.selectedProducts = [];
    },
    exportToJson() {
      const json = JSON.stringify(this.cart, null, 2);
      const blob = new Blob([json], { type: 'application/json' });
      saveAs(blob, 'purchase_report.json');
    },
    exportToCsv() {
      let csv = 'Product Name,Product Price,Purchase DateTime\n';
      this.cart.forEach(product => {
        csv += `${product.name},${product.price},${product.purchaseDateTime}\n`;
      });
      const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
      saveAs(blob, 'purchase_report.csv');
    },
    sortByName() {
      this.isNameSortedAsc = !this.isNameSortedAsc;
      this.sortCategoriesByName(this.categories, this.isNameSortedAsc);
    },
    sortByPrice() {
      this.isPriceSortedAsc = !this.isPriceSortedAsc;
      this.sortCategoriesByPrice(this.categories, this.isPriceSortedAsc);
    },
    sortCategoriesByName(categories, asc) {
      categories.forEach(category => {
        category.products.sort((a, b) => asc ? a.name.localeCompare(b.name) : b.name.localeCompare(a.name));
        if (category.categories.length) {
          this.sortCategoriesByName(category.categories, asc);
        }
      });
    },
    sortCategoriesByPrice(categories, asc) {
      categories.forEach(category => {
        category.products.sort((a, b) => asc ? a.price - b.price : b.price - a.price);
        if (category.categories.length) {
          this.sortCategoriesByPrice(category.categories, asc);
        }
      });
    }
  }
};
</script>
