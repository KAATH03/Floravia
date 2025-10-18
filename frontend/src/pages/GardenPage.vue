<template>
  <div class="garden-page">
    <!-- 🌸 Banner Section -->
    <section class="banner">
      <img src="@/assets/banner-flowers.png" alt="Banner Flowers" class="banner-img" />
      <div class="banner-text">
        <h1>Garden: Pick Your Flowers</h1>
      </div>
    </section>

    <!-- 🌸 Floral Divider -->
    <div class="floral-divider">
      <img src="@/assets/flower-divider.png" alt="Flower Divider" />
    </div>

    <!-- 🌿 Featured Section -->
    <section class="featured">
      <div class="featured-header">
        <div class="featured-oval">
          <h2>FEATURED</h2>
        </div>
      </div>

      <div class="featured-items">
        <div v-for="(flower, index) in featuredFlowers" :key="index" class="flower-card">
          <img
            :src="resolveImage(flower.image)"
            :alt="flower.name"
            @click="goToProduct(flower)"
            class="clickable"
          />
          <p class="name clickable" @click="goToProduct(flower)">
            {{ flower.name }}
          </p>

          <div class="price-row">
            <button
              class="basket-btn"
              :disabled="flower.quantity === 0"
              @click="addToBasket(flower)"
              :title="flower.quantity === 0 ? 'Out of Stock' : 'Add to Basket'"
            >
              <img src="@/assets/basket-cart.png" alt="Basket" class="basket-icon" />
            </button>
            <p class="price">
              <span v-if="flower.quantity === 0" style="color:#b22222;">Out of Stock</span>
              <span v-else>₱{{ flower.price }}</span>
            </p>
          </div>
        </div>
      </div>
    </section>

    <!-- 🌼 Category Section -->
    <section class="categories">
      <div class="category-buttons">
        <button
          v-for="(category, index) in categories"
          :key="index"
          :class="{ active: selectedCategory === category }"
          @click="selectCategory(category)"
        >
          {{ category }}
        </button>
      </div>

      <div class="category-items">
        <div v-for="(flower, index) in filteredFlowers" :key="index" class="flower-card">
          <img
            :src="resolveImage(flower.image)"
            :alt="flower.name"
            @click="goToProduct(flower)"
            class="clickable"
          />
          <p class="name clickable" @click="goToProduct(flower)">
            {{ flower.name }}
          </p>

          <div class="price-row">
            <button
              class="basket-btn"
              :disabled="flower.quantity === 0"
              @click="addToBasket(flower)"
              :title="flower.quantity === 0 ? 'Out of Stock' : 'Add to Basket'"
            >
              <img src="@/assets/basket-cart.png" alt="Basket" class="basket-icon" />
            </button>
            <p class="price">
              <span v-if="flower.quantity === 0" style="color:#b22222;">Out of Stock</span>
              <span v-else>₱{{ flower.price }}</span>
            </p>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'

const router = useRouter()

function goToProduct(flower) {
  router.push({
    path: "/singleproduct",
    query: { name: flower.name },
  });
}

// 🧺 Add to basket with stock check
async function addToBasket(flower) {
  try {
    const user = JSON.parse(localStorage.getItem("user"));
    if (!user || !user._id) {
      alert("⚠️ Please log in first!");
      return;
    }

    const res = await axios.get(`http://localhost:3000/api/products`);
    const product = res.data.find(
      (p) => p.bouquet_name?.toLowerCase() === flower.name.toLowerCase()
    );

    if (!product) return alert("❌ Product not found.");
    if (product.quantity <= 0) return alert(`❌ ${flower.name} is out of stock.`);

    await axios.post("http://localhost:3000/api/basket", {
      user_id: user._id,
      product_id: product._id,
      quantity: 1,
    });

    alert(`✅ ${flower.name} added to basket!`);
  } catch (error) {
    console.error("❌ Failed to add to basket:", error);
    const msg = error.response?.data?.message || "Failed to add item to basket.";
    alert(`❌ ${msg}`);
  }
}

// 🌸 FEATURED — keep your original 3 featured items
const featuredFlowers = ref([
  { name: 'Pink Carnations', category: 'Romance', price: 1800, image: 'pink-carnations.png', quantity: 10 },
  { name: 'Blue Hydrangeas', category: 'Romance', price: 1800, image: 'blue-hydrangeas.png', quantity: 10 },
  { name: 'Lysianthus', category: 'Romance', price: 1800, image: 'lysianthus.png', quantity: 10 },
])

// 🌿 CATEGORY setup
const categories = ref(['Birthday', 'Romance', 'Congratulation', 'Sympathy'])
const selectedCategory = ref('Birthday')

// 🌼 LOCAL FLOWERS — keep your original full set
const flowers = ref([
  // ❤️ Romance
  { name: 'Sunflower Bliss', category: 'Romance', price: 1200, image: 'sunflowerbliss.jpg', quantity: 8 },
  { name: 'Red Roses', category: 'Romance', price: 1800, image: 'red-roses.png', quantity: 5 },
  { name: 'Spring Harmony Tulips', category: 'Romance', price: 2000, image: 'springharmonytulips.jpg', quantity: 7 },
  { name: 'Red Carnations', category: 'Romance', price: 1800, image: 'red-carnations.png', quantity: 10 },
  { name: 'Pink Carnations', category: 'Romance', price: 1800, image: 'pink-carnations.png', quantity: 9 },
  { name: 'Blue Hydrangeas', category: 'Romance', price: 1800, image: 'blue-hydrangeas.png', quantity: 6 },
  { name: 'Lysianthus', category: 'Romance', price: 1800, image: 'lysianthus.png', quantity: 8 },

  // 🎂 Birthday
  { name: 'Lavender Tulip', category: 'Birthday', price: 2000, image: 'lavendertulip.jpg', quantity: 5 },
  { name: 'Pastel Bloom', category: 'Birthday', price: 1500, image: 'pastelbloom.jpg', quantity: 8 },
  { name: 'Sunshine Garden', category: 'Birthday', price: 1800, image: 'sunshinegarden.jpg', quantity: 6 },
  { name: 'Elegant Peach', category: 'Birthday', price: 1400, image: 'elegantpeach.jpg', quantity: 10 },

  // 🎉 Congratulation
  { name: 'Blush Elegance', category: 'Congratulation', price: 1700, image: 'blushelegance.jpg', quantity: 4 },
  { name: 'Golden Glow', category: 'Congratulation', price: 1600, image: 'goldenglow.jpg', quantity: 5 },
  { name: 'Blue Serenity', category: 'Congratulation', price: 1400, image: 'blueserenity.jpg', quantity: 5 },
  { name: 'Pink Basket Bloom', category: 'Congratulation', price: 1200, image: 'pinkbasketbloom.jpg', quantity: 3 },

  // 🕊️ Sympathy
  { name: 'Gentle Grace', category: 'Sympathy', price: 1800, image: 'gentlegrace.jpg', quantity: 6 },
  { name: 'Ivory Comfort', category: 'Sympathy', price: 1500, image: 'ivorycomfort.jpg', quantity: 4 },
  { name: 'Eternal Love', category: 'Sympathy', price: 2300, image: 'eternallove.jpg', quantity: 5 },
  { name: 'White Serenity', category: 'Sympathy', price: 1600, image: 'whiteserenity.jpg', quantity: 6 },
])

// 🌻 Image resolver
function resolveImage(imagePath) {
  if (!imagePath) return new URL("@/assets/placeholder.jpg", import.meta.url).href;
  if (imagePath.startsWith("/uploads/")) return `http://localhost:3000${imagePath}`;
  if (imagePath.startsWith("http")) return imagePath;
  const clean = imagePath.replace(/^\/+/, "");
  return new URL(`../assets/${clean}`, import.meta.url).href;
}

// 🌺 Load DB products — merge, not replace
onMounted(async () => {
  try {
    const res = await axios.get('http://localhost:3000/api/products');
    const dbFlowers = res.data.map(f => ({
      name: f.bouquet_name?.trim() || f.name,
      category: f.category || 'Romance',
      price: f.price,
      image: f.image || f.product_image || '',
      quantity: f.quantity ?? 0,
    }));

    const existingNames = flowers.value.map(f => f.name.toLowerCase());
    const unique = dbFlowers.filter(f => !existingNames.includes(f.name.toLowerCase()));

    if (unique.length > 0) {
      flowers.value.push(...unique);
      console.log('✅ Added new DB flowers:', unique);
    } else {
      console.log('🌸 No new DB products to add.');
    }
  } catch (err) {
    console.error('❌ Error loading DB products:', err);
  }
});

// 🌺 Filter by category
const filteredFlowers = computed(() =>
  flowers.value.filter(
    f => f.category?.toLowerCase() === selectedCategory.value.toLowerCase()
  )
);

function selectCategory(category) {
  selectedCategory.value = category;
}
</script>


<style scoped>
.garden-page {
  background-color: #fdf7f4;
  text-align: center;
  font-family: 'Outfit', sans-serif;
  margin: 0;
  padding: 0;
}

/* Make names look clickable */
.name {
  color: #9a9d68;
  font-weight: 600;
  margin: 1.5rem 0 0.8rem;
  font-size: 22px;
  cursor: pointer;
  transition: 0.2s ease;
}
.name:hover {
  text-decoration: underline;
  text-underline-offset: 3px;
}

/* Add general clickable animation */
.clickable {
  cursor: pointer;
  transition: transform 0.2s;
}
.clickable:hover {
  transform: scale(1.05);
}

/* 🌷 Banner */
.banner { position: relative; }
.banner-img {
  width: 100%;
  height: 30%;
  object-fit: cover;
  filter: brightness(85%);
  display: block;
}
.banner-text {
  position: absolute;
  top: 40%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: white;
}
.banner-text h1 { font-size: 74px; margin: 0; }

/* 🌸 Floral Divider */
.floral-divider img {
  width: 100%;
  height: auto;
  display: block;
}

/* 🌿 Featured Section */
.featured {
  background-color: #ffffff;
  margin: 0;
  padding: 0;
}
.featured-header {
  background-color: #9a9d68;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 4rem 0;
  position: relative;
  margin-top: 0;
}
.featured-oval {
  background-color: #ffffff;
  width: 1100px;
  height: 180px;
  border-radius: 50%;
  border: 5px solid #b8a0b8;
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.1);
}
.featured-oval h2 {
  color: #b8a0b8;
  font-family: 'Caprasimo', sans-serif;
  font-size: 52px;
  font-weight: 600;
  margin: 0;
  letter-spacing: 2px;
}

/* 🌼 Flower Cards */
.featured-items {
  background-color: #ffffff;
  padding: 3rem 0;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 2.5rem;
}
.flower-card {
  background-color: #ffffff;
  margin: 60px 20px;
  width: 340px;
  padding: 1.5rem 1.5rem 2rem 1.5rem;
  box-shadow: 0 6px 14px rgba(0, 0, 0, 0.15);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  text-align: center;
  border-radius: 10px;
}
.flower-card:hover {
  transform: scale(1.05);
  box-shadow: 0 8px 18px rgba(0, 0, 0, 0.2);
}
.flower-card > img {
  width: 100%;
  height: 340px;
  object-fit: cover;
  border-radius: 10px;
}

/* 🧺 Basket and Price */
.price-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 85%;
  margin: 0.8rem auto 0;
}
.basket-btn {
  background: transparent;
  border: none;
  cursor: pointer;
  padding: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 80px;
  height: 80px;
  border-radius: 50%;
  transition: background-color 0.3s ease, transform 0.2s ease;
}
.basket-btn:hover,
.basket-btn:active {
  background-color: #f8d2bb;
  transform: scale(1.08);
}
.basket-icon {
  width: 50px;
  height: 50px;
  object-fit: contain;
  transition: transform 0.2s ease;
}
.basket-btn:hover .basket-icon { transform: scale(1.1); }
.price {
  color: #5b6239;
  font-weight: 700;
  font-size: 24px;
  margin: 0;
}

/* 🌺 Categories */
.categories {
  background-color: #f9efe6;
  padding: 3rem 0;
}
.category-buttons {
  display: flex;
  justify-content: center;
  gap: 1.5rem;
  flex-wrap: wrap;
  margin: 50px 0 3rem;
}
.category-buttons button {
  border: 5px solid #a8b9c3;
  background: #f9efe6;
  color: #a8b9c3;
  font-weight: 400;
  font-size: 34px;
  font-family: 'Caprasimo';
  border-radius: 50px;
  padding: 1rem 2.5rem;
  cursor: pointer;
  transition: 0.3s ease;
}
.category-buttons button.active { border-color: #b8a0b8; color: #b8a0b8; }
.category-buttons button:hover { transform: scale(1.05); }
.category-items {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 3rem;
}
</style>
