<template>
  <header>
    <nav class="navbar navbar-expand-lg header">
      <div class="container">
        <!-- Logo -->
        <a class="navbar-brand logo d-flex align-items-center" href="#">
          B<span>M</span>
        </a>

        <!-- Toggler for sidebar -->
        <button
          class="navbar-toggler"
          type="button"
          @click="toggleSidebar"
        >
          <span class="navbar-toggler-icon"></span>
        </button>

        <!-- Navbar links for desktop -->
        <div class="collapse navbar-collapse justify-content-end d-none d-lg-flex">
          <ul class="navbar-nav">
            <li class="nav-item"><a class="nav-link" href="#">Home</a></li>
            <li class="nav-item"><a class="nav-link" href="#features">Features</a></li>
            <li class="nav-item"><a class="nav-link" href="#contact">Contact</a></li>
          </ul>
        </div>
      </div>
    </nav>

    <!-- Sidebar for mobile -->
    <div
      class="offcanvas offcanvas-start"
      tabindex="-1"
      :class="{ 'show': isSidebarOpen }"
      style="visibility: visible;"
      @click.self="toggleSidebar"
    >
      <div class="offcanvas-header">
        <h5 class="offcanvas-title text-white">Menu</h5>
        <button type="button" class="btn-close btn-close-white" @click="toggleSidebar"></button>
      </div>
      <div class="offcanvas-body d-flex flex-column">
        <ul class="navbar-nav">
          <li class="nav-item"><a class="nav-link text-white" href="#" @click="toggleSidebar">Home</a></li>
          <li class="nav-item"><a class="nav-link text-white" href="#features" @click="toggleSidebar">Features</a></li>
          <li class="nav-item"><a class="nav-link text-white" href="#contact" @click="toggleSidebar">Contact</a></li>
        </ul>
      </div>
    </div>
  </header>
</template>

<script>
export default {
  name: "AppHeader",
  data() {
    return {
      isSidebarOpen: false,
    };
  },
  methods: {
    toggleSidebar() {
      this.isSidebarOpen = !this.isSidebarOpen;
      // Add Bootstrap body class for offcanvas
      document.body.classList.toggle("offcanvas-backdrop", this.isSidebarOpen);
    },
  },
};
</script>

<style scoped>
.header {
  background: transparent;
  position: absolute;
  width: 100%;
  top: 0;
  left: 0;
  z-index: 1000;
}

.logo {
  font-family: "Pacifico", cursive;
  color: var(--color-cream);
  font-size: 1.5rem;
}
.logo span {
  color: var(--color-deep-red);
}

/* Override Bootstrap offcanvas background */
.offcanvas {
  background-color: var(--color-dark-gray);
  width: 250px;
}
.nav-link {
  font-weight: 600;
  color: var(--color-cream);
}
.nav-link:hover {
  color: var(--color-deep-red);
}
</style>
