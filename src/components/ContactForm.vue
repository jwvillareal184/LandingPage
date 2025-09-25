<template>
  <section id="contact" class="py-5 min-vh-100 d-flex align-items-center">
    <div class="container">
      <div class="row">
        <!-- Contact Info Column -->
        <div class="col-md-5 mb-4 slide-left">
          <div class="contact-info p-4 h-100">
            <h2 class="mb-3">Get in Touch</h2>
            <p><strong>Address:</strong> 123 Main St, Manila, Philippines</p>
            <p><strong>Email:</strong> info@mystore.com</p>
            <p><strong>Phone:</strong> +63 912 345 6789</p>
            <p><strong>Hours:</strong> Mon–Sat: 9am – 8pm</p>
          </div>
        </div>

        <!-- Form Column -->
        <div class="col-md-7 slide-right">
          <form @submit.prevent="submitForm" class="row g-3">
            <!-- First Name -->
            <div class="col-md-6 wrapper">
              <div class="inputGroup">
                <input
                  v-model="firstName"
                  type="text"
                  class="inputField"
                  placeholder="First Name"
                  required
                />
                <label class="inputLabel">First Name</label>
              </div>
            </div>

            <!-- Last Name -->
            <div class="col-md-6 wrapper">
              <div class="inputGroup">
                <input
                  v-model="lastName"
                  type="text"
                  class="inputField"
                  placeholder="Last Name"
                  required
                />
                <label class="inputLabel">Last Name</label>
              </div>
            </div>

            <!-- Email -->
            <div class="col-md-6 wrapper">
              <div class="inputGroup">
                <input
                  v-model="email"
                  type="email"
                  class="inputField"
                  placeholder="Email"
                  required
                />
                <label class="inputLabel">Email</label>
              </div>
            </div>

            <!-- Contact Number -->
            <div class="col-md-6 wrapper">
              <div class="inputGroup">
                <input
                  v-model="contactNumber"
                  type="tel"
                  class="inputField"
                  placeholder="Contact Number"
                  required
                />
                <label class="inputLabel">Contact Number</label>
              </div>
            </div>

            <!-- Title -->
            <div class="col-12 wrapper">
              <div class="inputGroup">
                <input
                  v-model="title"
                  type="text"
                  class="inputField"
                  placeholder="Title"
                  required
                />
                <label class="inputLabel">Title</label>
              </div>
            </div>

            <!-- Message -->
            <div class="col-12 wrapper">
              <div class="inputGroup">
                <textarea
                  v-model="message"
                  class="inputField"
                  rows="4"
                  placeholder="Your Message"
                  required
                ></textarea>
                <label class="inputLabel">Your Message</label>
              </div>
            </div>

            <!-- Submit Button -->
            <div class="col-12 text-center">
              <button type="submit" class="btn btn-custom px-5 py-2 w-100">
                Submit
              </button>
            </div>
          </form>

          <!-- Success alert -->
          <div
            v-if="submitted"
            class="alert alert-success alert-dismissible fade show mt-4"
            role="alert"
          >
            🎉 Thank you, <strong>{{ firstName }}</strong>! We'll get back to you soon.
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="alert"
              aria-label="Close"
            ></button>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: "ContactForm",
  data() {
    return {
      firstName: "",
      lastName: "",
      email: "",
      contactNumber: "",
      title: "",
      message: "",
      submitted: false,
    };
  },
  methods: {
    submitForm() {
      if (
        this.firstName &&
        this.lastName &&
        this.email &&
        this.contactNumber &&
        this.title &&
        this.message
      ) {
        this.submitted = true;
      }
    },
  },
  mounted() {
    const options = {
      root: null, 
      threshold: 0.2, 
    };

    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          entry.target.classList.add("show"); // slide in
        } else {
          entry.target.classList.remove("show"); // slide out
        }
      });
    }, options);

    const left = this.$el.querySelector(".slide-left");
    const right = this.$el.querySelector(".slide-right");
    if (left) observer.observe(left);
    if (right) observer.observe(right);
  }
};
</script>

<style scoped>
#contact {
  background-color: var(--color-cream); 
  border-radius: 8px;
  overflow-x: hidden;
}
.contact-info {
  background: var(--color-cream);
  border: 1px solid var(--color-cream);
  border-radius: 8px;
  color: var(--color-deep-red);
}
.btn-custom {
  background-color: var(--color-deep-red);
  color: var(--color-cream);
  border: none;
  border-radius: 30px;
  font-weight: 600;
  transition: background-color 0.3s ease;
}
.btn-custom:hover {
  background-color: var(--color-dark-gray);
  color: var(--color-cream);
}
.wrapper {
  margin-bottom: 1rem;
  font-family: var(--font-fira-sans);
}
.inputGroup {
  position: relative;
  width: 100%;
}
.inputField {
  width: 100%;
  padding: 12px;
  font-size: 1rem;
  border: 1px solid var(--color-deep-red);
  border-radius: 6px;
  outline: none;
  background: var(--color-cream);
  transition: all 0.3s ease; 
}
.inputField:hover {
  border-color: var(--color-dark-gray);
  color: var(--color-cream); 
  box-shadow: 0 0 8px var(--color-deep-red);
}
.inputField:focus {
  border-color: var(--color-deep-red);
}
.inputField::placeholder {
  color: transparent; 
}
.inputLabel {
  position: absolute;
  top: 12px;
  left: 12px;
  color: var(--color-deep-red);
  font-size: 1rem;
  pointer-events: none;
  transition: all 0.2s ease-out;
  background: var(--color-cream);
  padding: 0 4px;
  border-radius: 4px;
}
.inputField:focus + .inputLabel,
.inputField:not(:placeholder-shown) + .inputLabel {
  top: -8px;
  left: 8px;
  font-size: 0.75rem;
  color: var(--color-deep-red);
}
.slide-left, .slide-right {
  opacity: 0;
  transition: all 0.8s ease;
}

.slide-left {
  transform: translateX(-50px);
}

.slide-right {
  transform: translateX(50px);
}

.slide-left.show,
.slide-right.show {
  opacity: 1;
  transform: translateX(0);
}

</style>
