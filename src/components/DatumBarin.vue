<template>
  <v-app>
    <layout>
      <v-app-bar class="pt-10 mb-10" scroll-behavior="elevate" color="white">
        <template v-slot:prepend>
          <v-img
            :width="50"
            :height="50"
            class="rounded-lg"
            src="../assets/magamart.jpg"
          ></v-img>
        </template>
        <v-title class="custom-font-size-title">Mega Mart</v-title>
        <v-spacer></v-spacer>

        <div class="search-container">
          <v-row no-gutters align="center">
            <v-col cols="12" md="3">
              <v-select
                v-model="selectedCategory"
                :items="categories"
                outlined
                dense
              />
            </v-col>
            <v-col cols="12" md="9">
              <v-text-field
                v-model="searchText"
                label="Search here..."
                append-inner-icon="mdi-magnify"
                outlined
                dense
              ></v-text-field>
            </v-col>
          </v-row>
        </div>
        <v-spacer></v-spacer>
        <v-icon color="blue" icon="mdi-cart" size="x-large"></v-icon>
        <v-badge color="blue" :content="cartItemCount" inline>Cart ok</v-badge>
        <v-spacer></v-spacer>
      </v-app-bar>
    </layout>
    <main class="mt-15 pt-15">
      <div style="background-color: lightgrey">
        <h1 style="color: #1e88e5" class="mt-6 mb-4">Result</h1>
        <v-container>
          <v-row>
            <v-col
              cols="12"
              md="6"
              lg="3"
              v-for="(product, index) in filteredProducts"
              :key="product.id"
            >
              <v-card
                max-width="300"
                @mouseenter="toggleHover(product, true)"
                @mouseleave="toggleHover(product, false)"
                class="hover-card"
              >
                <div class="d-flex flex-column align-center">
                  <v-img
                    class="text-center"
                    :src="product.image"
                    :alt="product.title"
                    gradient="to bottom, rgba(0,0,0,.1), rgba(0,0,0,.5)"
                    height="200px"
                    width="200px"
                    cover
                  >
                  </v-img>
                </div>

                <div class="d-flex flex-column">
                  <v-card-title class="text-grey">{{
                    product.title
                  }}</v-card-title>
                  <v-card-subtitle class="text-black"
                    >$ {{ product.price }}</v-card-subtitle
                  >
                  <v-card-text>
                    <v-rating
                      v-model="product.rating"
                      hover
                      half-increments
                      bg-color="orange lighten-1"
                      color="yellow"
                    ></v-rating>
                  </v-card-text>
                  <v-card-actions>
                    <div v-if="product.hover" class="vertical-button-container">
                      <v-btn color="primary" @click="addToCart(product)">
                        {{
                          product.status === "cart"
                            ? "Add to Cart"
                            : "Purchased"
                        }}
                      </v-btn>
                    </div>
                  </v-card-actions>
                </div>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </div>
    </main>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      categories: [
        "All",
        "Games",
        "Laptop",
        "Mobiles",
        "Book",
        "Deals",
        "women's clothing",
        "electronics",
      ],
      selectedCategory: "All",
      searchText: "",
      products: [],
      rating: "",
      cartItemCount: 0,
    };
  },

  async mounted() {
    this.fetchProducts();
  },

  computed: {
    screenSize() {
      const screenSize = window.innerWidth;

      const cols = screenSize < 768 ? 1 : 2;

      return this.products.filter((product, index) => index % cols === 0);
    },
    filteredProducts() {
      let filtered = this.products;

      if (this.selectedCategory !== "All") {
        filtered = filtered.filter(
          (product) => product.category === this.selectedCategory
        );
      }

      if (this.searchText) {
        const searchLower = this.searchText.toLowerCase();
        filtered = filtered.filter(
          (product) =>
            product.title.toLowerCase().includes(searchLower) ||
            product.description.toLowerCase().includes(searchLower)
        );
      }

      return filtered;
    },
  },

  methods: {
    async fetchProducts() {
      const url = `https://fakestoreapi.com/products`;
      try {
        const response = await fetch(url);
        const data = await response.json();
        data.forEach((product) => {
          product.rating = Math.random() * (5 - 3) + 3;
          product.status = "cart";
        });

        this.products = data;
      } catch (error) {
        console.error("Error fetching products:", error);
      }
    },
    toggleHover(product, value) {
      product.hover = value;
    },
    toggleProductStatus(product) {
      product.status = product.status === "cart" ? "purchased" : "cart";
    },
    addToCart(product) {
      if (product.status === "cart") {
        this.cartItemCount++;
        product.status = "purchased";
      }
    },
  },
};
</script>

<style>
.custom-font-size-title {
  font-size: 28px;
  font-weight: 600;
  color: #1e88e5;
  margin-left: 10px;
}

.search-container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 10px;
  width: 400px;
}

.search-container > .searchall {
  border: 3px solid black;
  border-radius: 10px;
  width: 100%;
  height: 60px;
  padding: 0 10px;
  box-sizing: border-box;
}

@media (max-width: 768px) {
  .search-container > .searchall {
    height: 30px;
  }
}
.hover-card {
  transition: transform 0.3s, box-shadow 0.3s;
  position: relative;
}

.hover-card:hover {
  transform: scale(1.05);
  box-shadow: 0px 2px 10px rgba(0, 0, 0, 0.2);
}

.vertical-button-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: rgba(255, 255, 255, 0.9);
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
  opacity: 0;
  transition: opacity 0.3s;
}

.hover-card:hover .vertical-button-container {
  opacity: 1;
}

.vertical-button-container > v-btn {
  width: 100%;
}
</style>
