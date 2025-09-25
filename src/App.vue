<template>
  <div id="app">
    <AppHeader />
    <HeroSection />
    <FeaturesSection />
    <ContactForm />
    <AppFooter />
  </div>
</template>

<script>
import AppHeader from "./components/AppHeader.vue";
import HeroSection from "./components/HeroSection.vue";
import FeaturesSection from "./components/FeaturesSection.vue";
import ContactForm from "./components/ContactForm.vue";
import AppFooter from "./components/AppFooter.vue";

export default {
  name: "App",
  components: {
    AppHeader,
    HeroSection,
    FeaturesSection,
    ContactForm,
    AppFooter,
  },
};
</script>

<style>
:root {
  --color-deep-red: #862a33;   
  --color-dark-gray: #2c2a33;  
  --color-cream: #f7f7f1;       
  --color-gray: #8a8787; 
  --color-white: #ffffff;
}
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Open Sans';
  overflow-x: hidden;
}
</style>
