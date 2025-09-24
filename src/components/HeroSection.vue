<template>
  <section class="hero d-flex align-items-center position-relative">
    <!-- Bootstrap Carousel as Background -->
    <div id="heroCarousel" class="carousel slide carousel-fade position-absolute w-100 h-100" data-bs-ride="carousel">
      <div class="carousel-inner h-100">
        <div class="carousel-item active h-100">
          <img src="@/assets/image1.jpg" class="d-block w-100 h-100 object-fit-cover" alt="Slide 1">
        </div>
        <div class="carousel-item h-100">
          <img src="@/assets/image2.jpg" class="d-block w-100 h-100 object-fit-cover" alt="Slide 2">
        </div>
        <div class="carousel-item h-100">
          <img src="@/assets/image3.jpg" class="d-block w-100 h-100 object-fit-cover" alt="Slide 3">
        </div>
      </div>
    </div>

    <!-- Overlay to darken for text readability -->
    <div class="overlay"></div>

    <!-- Slide-in content -->
    <div :class="['container text-start text-white slide-in-container', { show: showContainer }]">
      <h2 class="fw-bold">{{ welcomeMessage }}</h2>

      <!-- Pop container -->
      <div v-if="isVisible" :class="['mt-3', popClass]">
        <div class="card card-body bg-transparent border-0 p-0 text-white">
          <p class="lead mb-0">Shopping made easy, living made breezy.</p>
        </div>
      </div>

      <button class="btn pill-btn mt-3" @click="toggleAbout">
        {{ showAbout ? "Hide" : "Learn More" }}
      </button>
    </div>
  </section>
</template>

<script>
export default {
  name: "HeroSection",
  data() {
    return {
      welcomeMessage: "Welcome to BreezeMall Shopping!",
      showAbout: false,
      showContainer: false,
      isVisible: false,
      popClass: '',
    };
  },
  mounted() {
    setTimeout(() => {
      this.showContainer = true;
    }, 100);
  },
  methods: {
    toggleAbout() {
      if (!this.showAbout) {
        this.isVisible = true;
        this.popClass = 'pop-in';
      } else {
        this.popClass = 'pop-out';
        setTimeout(() => {
          this.isVisible = false;
        }, 400);
      }
      this.showAbout = !this.showAbout;
    },
  },
};
</script>

<style scoped>
.hero {
  height: 100vh;
  overflow: hidden;
}

/* Dark overlay */
.overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(to right, rgba(0,0,0,0.6) 40%, rgba(0,0,0,0) 100%);
  z-index: 1;
}

/* Ensure carousel stays in the back */
.carousel,
.carousel-inner,
.carousel-item,
.carousel-item img {
  height: 100%;
}
.carousel {
  z-index: 0;
}
.carousel-item img {
  object-fit: cover;
}

/* Foreground content */
.slide-in-container {
  z-index: 2;
  transform: translateX(-100%);
  opacity: 0;
  transition: transform 0.8s ease, opacity 0.8s ease;
}
.slide-in-container.show {
  transform: translateX(0);
  opacity: 1;
}

/* Button styling */
.pill-btn {
  background-color: var(--color-deep-red);
  color: var(--color-cream);
  border-radius: 50px;
  padding: 0.5rem 1.5rem;
  font-weight: 600;
  transition: all 0.3s ease;
  border: none;
}
.pill-btn:hover {
  background-color: var(--color-grey);
  transform: scale(1.05);
  color: var(--color-cream);
}

/* Pop animations */
.pop-in {
  animation: popIn 0.4s forwards;
}
.pop-out {
  animation: popOut 0.4s forwards;
}
@keyframes popIn {
  0% { transform: scale(0.5); opacity: 0; }
  70% { transform: scale(1.2); opacity: 1; }
  100% { transform: scale(1); opacity: 1; }
}
@keyframes popOut {
  0% { transform: scale(1); opacity: 1; }
  30% { transform: scale(1.2); opacity: 1; }
  100% { transform: scale(0.5); opacity: 0; }
}
</style>
