<template>
  <section id="features" class="py-5 min-vh-100 d-flex align-items-center">
    <div class="container my-5">
      <h3 class="text-center mb-4">Explore Our Collections</h3>

      <!-- Loading skeleton -->
      <div v-if="loading">
        <div class="row g-4">
          <div v-for="n in perPage" :key="n" class="col-md-4 col-sm-6">
            <div class="card h-100 shadow-sm">
              <div class="card-img-top bg-secondary placeholder" style="height:200px;"></div>
              <div class="card-body d-flex flex-column">
                <h5 class="card-title placeholder-glow">
                  <span class="placeholder col-8"></span>
                </h5>
                <p class="card-text placeholder-glow">
                  <span class="placeholder col-12"></span>
                  <span class="placeholder col-10"></span>
                </p>
                <p class="fw-bold text-success mb-2 placeholder-glow">
                  <span class="placeholder col-4"></span>
                </p>
                <button class="btn btn-warning disabled placeholder col-6 mt-auto"></button>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Error message -->
      <div v-else-if="error" class="text-center py-5 text-danger">
        <h5>{{ error }}</h5>
      </div>

      <!-- Grid of features -->
      <div v-else>
        <!-- Category Tabs + Rating Filter -->
        <div class="d-flex justify-content-center  mb-4 gap-2 flex-wrap">
          <div class="d-flex gap-2 flex-wrap justify-content-center">
            <button
              v-for="cat in categories"
              :key="cat"
              class="btn"
              :class="{
                'btn-secondary': selectedCategory === cat,
                'btn-outline-secondary': selectedCategory !== cat
              }"
              @click="selectedCategory = cat"
            >
              {{ cat.charAt(0).toUpperCase() + cat.slice(1) }}
            </button>
            <select v-model="selectedRating" class="form-select w-auto">
              <option value="all">All Ratings</option>
              <option v-for="n in 5" :key="n" :value="n">{{ n }}★</option>
            </select>
          </div>
        </div>

        <div v-if="paginatedFeatures.length === 0" class="text-center py-5">
          <h5>No products found for this category/rating.</h5>
        </div>

        <div v-else class="row g-4">
          <div
            v-for="feature in paginatedFeatures"
            :key="feature.id"
            class="col-md-4 col-sm-6"
          >
            <div class="card h-100 shadow-sm animate-card">
              <img
                :src="feature.image"
                class="card-img-top img-fluid object-fit-contain bg-light"
                alt="Product Image"
                style="height:200px; width:100%;"
                loading="lazy"
              />
              <div class="card-body d-flex flex-column">
                <h5 class="card-title">{{ feature.name }}</h5>

                <!-- Rating stars -->
                <div class="mb-2 d-flex align-items-center" v-if="feature.rating">
                  <span v-for="n in feature.stars.full" :key="'f'+n" class="text-warning">★</span>
                  <span v-if="feature.stars.half" class="text-warning">☆</span>
                  <span v-for="n in feature.stars.empty" :key="'e'+n" class="text-secondary">★</span>
                  <small class="ms-2 text-muted">({{ feature.rating.count }})</small>
                </div>

                <p class="fw-bold text-success mb-2">
                  ${{ feature.price.toFixed(2) }}
                </p>
                <button class="btn buy-btn mt-auto" @click="openModal(feature)">
                  Buy Now
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- Pagination Controls -->
        <div class="d-flex justify-content-center align-items-center mt-4 gap-2">
          <button
            class="btn btn-outline-secondary"
            :disabled="currentPage === 1"
            @click="prevPage"
          >
            Previous
          </button>
          <span>Page {{ currentPage }} of {{ totalPages }}</span>
          <button
            class="btn btn-outline-secondary"
            :disabled="currentPage === totalPages"
            @click="nextPage"
          >
            Next
          </button>
        </div>
      </div>

      <!-- Buy Now Modal -->
      <div
        class="modal fade"
        id="buyNowModal"
        tabindex="-1"
        aria-hidden="true"
        ref="modal"
      >
        <div class="modal-dialog modal-dialog-centered modal-lg">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title text-capitalize">{{ selectedItem?.category }}</h5>
              <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body" v-if="selectedItem">
              <div class="row">
                <div class="col-md-5 d-flex justify-content-center align-items-center mb-3 mb-md-0">
                  <img
                    v-if="selectedItem"
                    :src="selectedItem.image"
                    class="img-fluid rounded"
                    alt="Selected product"
                    style="max-height: 250px; object-fit: contain;"
                  />
                </div>
                <div class="col-md-7">
                  <div class="p-3 border d-flex flex-column gap-1">
                    <h4>{{ selectedItem?.name }}</h4>

                    <!-- Modal Rating -->
                    <div class="d-flex align-items-center gap-1" v-if="selectedItem?.rating">
                      <span v-for="n in selectedItem.stars.full" :key="'f'+n" class="text-warning">★</span>
                      <span v-if="selectedItem.stars.half" class="text-warning">☆</span>
                      <span v-for="n in selectedItem.stars.empty" :key="'e'+n" class="text-secondary">★</span>
                      <small class="ms-2 text-muted">({{ selectedItem.rating.count }})</small>
                    </div>

                    <p class="fw-bold text-success">Price: ${{ selectedItem?.price.toFixed(2) }}</p>

                    <p class="mb-2">
                      {{ truncatedDescription(selectedItem) }}
                      <span v-if="selectedItem?.description?.length > 150"
                            @click="toggleDescription(selectedItem)"
                            class="see-more">
                        {{ selectedItem.showFull ? ' See Less' : '... See More' }}
                      </span>
                    </p>

                    <div class="d-flex align-items-center gap-2">
                      <label for="quantity" class="mb-0">Quantity:</label>
                      <button class="btn btn-outline-secondary btn-sm" type="button" @click="decreaseQuantity">−</button>
                      <input id="quantity" type="number" min="1" v-model.number="quantity" class="form-control w-25 text-center" readonly />
                      <button class="btn btn-outline-secondary btn-sm" type="button" @click="increaseQuantity">+</button>
                    </div>

                    <div class="d-flex justify-content-between align-items-center mt-2">
                      <p class="fw-bold mb-0">Total: ${{ (selectedItem?.price * quantity).toFixed(2) }}</p>
                      <button class="btn btn-success" @click="confirmOrder">
                        <i class="bi bi-cart-check" style="font-size: 1.2rem;"></i>
                      </button>
                    </div>

                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!--Toast -->
      <div class="toast-container position-fixed bottom-0 end-0 p-3">
        <div id="orderToast" class="toast align-items-center text-bg-success border-0" role="alert" aria-live="assertive" aria-atomic="true" ref="orderToast">
          <div class="d-flex">
            <div class="toast-body">✅ Order placed successfully!</div>
            <button type="button" class="btn-close btn-close-white me-2 m-auto" data-bs-dismiss="toast" aria-label="Close"></button>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { Modal, Toast } from "bootstrap";

let cachedProducts = null;

export default {
  name: "FeaturesSection", // Component name

  // Reactive data for the component
  data() {
    return {
      features: [], // List of products/features fetched from API
      categories: [], // List of product categories for filtering
      selectedCategory: "all", // Currently selected category filter
      selectedRating: "all", // Currently selected rating filter
      loading: true, // Loading state while fetching products
      error: null, // Holds error message if API call fails
      currentPage: 1, // Current page for pagination
      perPage: 6, // Number of items per page
      observer: null, // IntersectionObserver for scroll animations
      selectedItem: null, // Item selected for modal view
      quantity: 1, // Quantity selected for ordering
      modalInstance: null, // Reference to Bootstrap modal instance
    };
  },

  // Computed properties for filtered and paginated data
  computed: {
    filteredFeatures() {
      // Filter by category first
      let filtered = this.selectedCategory === "all"
        ? this.features
        : this.features.filter(f => f.category === this.selectedCategory);

      // Filter by rating if applicable
      if (this.selectedRating !== "all") {
        // Only match whole number ratings
        filtered = filtered.filter(f => Math.floor(f.rating?.rate || 0) === Number(this.selectedRating));
      }
      return filtered;
    },

    // Calculate total number of pages for pagination
    totalPages() {
      return Math.ceil(this.filteredFeatures.length / this.perPage);
    },

    // Return only the features for the current page
    paginatedFeatures() {
      const start = (this.currentPage - 1) * this.perPage;
      return this.filteredFeatures.slice(start, start + this.perPage);
    },
  },

  // Watchers for reactive changes
  watch: {
    selectedCategory() { this.currentPage = 1; }, // Reset to page 1 when category changes
    selectedRating() { this.currentPage = 1; },   // Reset to page 1 when rating changes
    paginatedFeatures() { this.$nextTick(() => this.observeCards()); }, // Observe newly rendered cards
  },

  // Component methods
  methods: {
    // Go to next page
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
        this.$nextTick(() => { this.observeCards(); this.scrollToTop(); });
      }
    },

    // Go to previous page
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
        this.$nextTick(() => { this.observeCards(); this.scrollToTop(); });
      }
    },

    // Scroll to the first visible card
    scrollToTop() {
      const firstCard = this.$el.querySelector(".animate-card");
      if (firstCard) firstCard.scrollIntoView({ behavior: "smooth", block: "start" });
    },

    // Open modal and initialize quantity
    openModal(item) {
      this.selectedItem = item;
      this.quantity = 1;
      if (!this.modalInstance) this.modalInstance = new Modal(this.$refs.modal); // Initialize Bootstrap modal
      this.modalInstance.show();
    },

    // Observe cards for scroll animations using IntersectionObserver
    observeCards() {
      if (this.observer) this.observer.disconnect();
      this.observer = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
          entry.target.classList.toggle("visible", entry.isIntersecting);
          entry.target.classList.toggle("hidden", !entry.isIntersecting);
        });
      }, { threshold: 0.1 });

      const cards = this.$el.querySelectorAll(".animate-card");
      cards.forEach(card => this.observer.observe(card));
    },

    // Fetch products from API or cache
    async fetchProducts() {
      try {
        this.loading = true;

        // Use cached products if available
        if (cachedProducts) {
          this.features = cachedProducts;
        } else {
          const res = await fetch("https://fakestoreapi.com/products");
          const data = await res.json();

          // Map API data to internal structure
          cachedProducts = data.map(item => {
            const rate = item.rating?.rate || 0;
            const stars = this.getStars(rate);
            return { 
              id: item.id,
              name: item.title,
              description: item.description,
              image: item.image,
              price: item.price,
              rating: item.rating || { rate: 0, count: 0 },
              showFull: false, // For toggling description
              category: item.category,
              stars, // Precomputed stars
            };
          });

          this.features = cachedProducts;
        }

        // Generate unique categories with "all" option
        this.categories = ["all", ...new Set(this.features.map(f => f.category))];

      } catch (err) {
        console.error(err);
        this.error = "Failed to load products.";
      } finally {
        this.loading = false;
        this.$nextTick(() => this.observeCards());
      }
    },

    // Increase/decrease quantity
    increaseQuantity() { this.quantity++; },
    decreaseQuantity() { if (this.quantity > 1) this.quantity--; },

    // Confirm order and show toast
    confirmOrder() {
      if (this.modalInstance) this.modalInstance.hide();
      const toastEl = this.$refs.orderToast;
      const toast = new Toast(toastEl);
      toast.show();
    },

    // Convert numeric rating to full/half/empty stars
    getStars(rate) {
      rate = rate || 0; // Null-safe
      const fullStars = Math.floor(rate);
      const halfStar = rate % 1 >= 0.5;
      const emptyStars = 5 - fullStars - (halfStar ? 1 : 0);
      return { full: fullStars, half: halfStar, empty: emptyStars };
    },

    // Return truncated description
    truncatedDescription(feature) {
      if (!feature) return '';
      if (!('showFull' in feature)) feature.showFull = false;
      return feature.showFull ? feature.description : feature.description.slice(0, 150);
    },

    // Toggle full/truncated description
    toggleDescription(item) { item.showFull = !item.showFull; },
  },

  // Lifecycle hook: fetch products when component mounts
  mounted() { this.fetchProducts(); },
};

</script>

<style scoped>
#features {
  background-color: var(--color-light-pink);
  padding: 2rem;
  border-radius: 12px;
}
.card-title {
  color: var(--color-dark-brown);
  font-weight: 600;
}
.animate-card {
  opacity: 0;
  transform: translateY(50px) scale(1);
  transition: all 0.6s ease-in-out;
  background-color: var(--color-light-pink);
}
.animate-card.visible {
  opacity: 1;
  transform: translateY(0) scale(1);
}
.animate-card.hidden {
  opacity: 0;
  transform: translateY(-50px) scale(1);
}
.animate-card:hover {
  transform: translateY(0) scale(1.05); 
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
}
.buy-btn {
  font-weight: 600;
  background-color: var(--color-dark-gray);
  color: var(--color-white);
  border: none;
  transition: background-color 0.3s ease;
}
.buy-btn:hover {
  background-color: var(--color-gray);
  color: var(--color-white);
}
.see-more {
  font-weight: 500;
  color: var(--color-gray);
  cursor:pointer;
}
</style>