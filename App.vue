<template>
  <div class="luxury-bg">
    <div class="card">

      <!-- HEADER -->
      <div class="header">
        <h1>MAY GINTO KANA MAY PILAK KAPA!</h1>
        <p>Gold & Silver kaba?</p>
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
    radial-gradient(circle at 20% 10%, rgba(28, 219, 11, 0.25), transparent 40%),
    radial-gradient(circle at 80% 90%, rgba(9, 190, 136, 0.15), transparent 45%),
    linear-gradient(145deg, #2a68a1, #d81a1a, #141414);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 2rem;
}

/* ===== CARD ===== */
.card {
  width: 90%;
  max-width: 1000px;
  background: rgba(18,18,18,0.9);
  backdrop-filter: blur(18px);
  border-radius: 0px;
  border: 1px solid rgba(100, 10, 17, 0.25);
  box-shadow: 0 40px 90px rgba(0,0,0,0.7);
  overflow: hidden;
}

/* ===== HEADER ===== */
.header {
  text-align: center;
  padding: 1.5rem;
  border-bottom: 1px solid rgba(19, 212, 67, 0.2);
}

.header h1 {
  font-size: 3rem;
  background: linear-gradient(90deg, #05bedf, #942929);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.header p {
  color: #d0e6ec;
  font-size: 1.85rem;
}

/* ===== CONTENT ===== */
.content {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax( 390px, 1fr));
  gap: 1.5rem;
  padding: 1.5rem;
}

/* ===== BOX ===== */
.box {
  background: rgba(7, 225, 233, 0.9);
  padding: 1.5rem;
  border-radius: 20px;
}

.box h3 {
  color: hsl(47, 82%, 2%);
  margin-bottom: 1rem;
}

/* ===== FORM ===== */
label {
  font-size: 1.8rem;
  color: #d47114d5;
}

select,
input {
  width: 100%;
  margin: 10px 0 16px;
  padding: 10px;
  background: #0b2718;
  color: #f7f7f7;
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
  color: #0e0b03;
  text-align: center;
}

small {
  display: block;
  text-align: center;
  color: #e0e5f1;
}
</style>
