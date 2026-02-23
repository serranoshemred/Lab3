<script>
import Calculator from './calculator.vue'

export default {
  components: {
    Calculator
  }
}
</script>

<template>
  <div>
    <h1>My App</h1>
    <Calculator />
  </div>
</template>

<template>
  <div class="luxury-bg">
    <div class="card">

      <!-- HEADER -->
      <div class="header">
        <h1>Luxury Jewelry Calculator</h1>
        <p>Gold & Silver Pricing • PHP</p>
      </div>

      <!-- CONTENT -->
      <div class="content">

        <!-- INPUT -->
        <div class="box">
          <h3>Item Details</h3>

          <label>Metal Type</label>
          <select v-model="metal">
            <option value="gold">Gold</option>
            <option value="silver">Silver</option>
          </select>

          <div v-if="metal === 'gold'">
            <label>Gold Karat</label>
            <select v-model="karat">
              <option value="24">24K – ₱9,790.57</option>
              <option value="22">22K – ₱8,975.57</option>
              <option value="18">18K – ₱7,343.94</option>
              <option value="10">10K – ₱4,081.29</option>
            </select>
          </div>

          <label>Weight (grams)</label>
          <input type="number" v-model.number="grams" min="0" />
        </div>

        <!-- RESULT -->
        <div class="box result">
          <h3>Price Breakdown</h3>

          <div class="row">
            <span>Material Cost</span>
            <span>₱ {{ animatedBase.toLocaleString() }}</span>
          </div>
          <div class="row">
            <span>Design Fee</span>
            <span>₱ 2,000</span>
          </div>
          <div class="row">
            <span>Tax (12%)</span>
            <span>₱ {{ animatedTax.toLocaleString() }}</span>
          </div>

          <div class="divider"></div>

          <div class="total">
            ₱ {{ animatedTotal.toLocaleString() }}
          </div>
          <small>Total Payable</small>
        </div>

      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

const metal = ref('gold')
const karat = ref(24)
const grams = ref(0)

const goldRates = {
  24: 9790.57,
  22: 8975.57,
  18: 7343.94,
  10: 4081.29
}

const silverRate = 75
const designFee = 2000
const taxRate = 0.12

const basePrice = computed(() =>
  metal.value === 'gold'
    ? grams.value * goldRates[karat.value]
    : grams.value * silverRate
)

const tax = computed(() => (basePrice.value + designFee) * taxRate)
const total = computed(() => basePrice.value + designFee + tax.value)

/* ===== PRICE ANIMATION ===== */
const animatedBase = ref(0)
const animatedTax = ref(0)
const animatedTotal = ref(0)

const animate = (refVal, target) => {
  const start = refVal.value
  const duration = 400
  const startTime = performance.now()

  const step = (time) => {
    const progress = Math.min((time - startTime) / duration, 1)
    refVal.value = Math.floor(start + (target - start) * progress)
    if (progress < 1) requestAnimationFrame(step)
  }
  requestAnimationFrame(step)
}

watch([basePrice, tax, total], () => {
  animate(animatedBase, basePrice.value)
  animate(animatedTax, tax.value)
  animate(animatedTotal, total.value)
})
</script>

<style scoped>
/* ===== BACKGROUND ===== */
.luxury-bg {
  min-height: 100vh;
  background:
    radial-gradient(circle at 20% 10%, rgba(255,215,0,0.25), transparent 40%),
    radial-gradient(circle at 80% 90%, rgba(255,215,0,0.15), transparent 45%),
    linear-gradient(145deg, #020202, #0b0b0b, #141414);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 2rem;
}

/* ===== CARD ===== */
.card {
  width: 100%;
  max-width: 900px;
  background: rgba(15,15,15,0.9);
  backdrop-filter: blur(16px);
  border-radius: 24px;
  border: 1px solid rgba(255,215,0,0.25);
  box-shadow: 0 30px 80px rgba(0,0,0,0.7);
  overflow: hidden;
}

/* ===== HEADER ===== */
.header {
  text-align: center;
  padding: 1.5rem;
  border-bottom: 1px solid rgba(255,215,0,0.2);
}

.header h1 {
  font-size: 3rem;
  background: linear-gradient(90deg, #facc15, #8af5fd);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.header p {
  color: #aaa;
  font-size: 0.85rem;
}

/* ===== CONTENT ===== */
.content {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
  gap: 1.5rem;
  padding: 1.5rem;
}

/* ===== BOX ===== */
.box {
  background: rgba(25,25,25,0.9);
  padding: 1.5rem;
  border-radius: 20px;
}

.box h3 {
  color: #facc15;
  margin-bottom: 1rem;
}

/* ===== FORM ===== */
label {
  font-size: 1.8rem;
  color: #ddd;
}

select,
input {
  width: 100%;
  margin: 6px 0 14px;
  padding: 10px;
  background: #0a0a0a;
  color: #fff;
  border: 1px solid #444;
  border-radius: 10px;
}

/* ===== RESULT ===== */
.result .row {
  display: flex;
  justify-content: space-between;
  font-size: 0.9rem;
  margin: 8px 0;
  color: #eee;
}

.divider {
  height: 1px;
  margin: 1rem 0;
  background: linear-gradient(90deg, transparent, rgb(136, 125, 65), transparent);
}

.total {
  font-size: 2rem;
  font-weight: bold;
  color: #facc15;
  text-align: center;
}

small {
  display: block;
  text-align: center;
  color: #aaa;
}
</style>
