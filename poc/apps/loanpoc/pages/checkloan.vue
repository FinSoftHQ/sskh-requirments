<template>
  <UContainer class="check-loan-container">
    <h1 class="title">ตรวจสอบสิทธิ์การขอสินเชื่อ</h1>
    <form @submit.prevent="checkEligibility" class="loan-form">
      <UFormGroup label="เงินเดือน" help="Enter your monthly salary.">
        <UInput type="number" v-model="salary" required />
      </UFormGroup>
      <UFormGroup label="เงินพิเศษ + ตกเบิก" help="Enter any additional monthly income.">
        <UInput type="number" v-model="additionalIncome" required />
      </UFormGroup>
      <UFormGroup label="รวมรายจ่าย" help="รวมรายจ่ายทั้งหมดต่อเดือน">
        <UInput type="number" v-model="expenses" required />
      </UFormGroup>
      <UFormGroup label="ตำแหน่ง" help="เลือกตำแหน่งงานของท่าน">
        <USelect v-model="memberType" :options="memberTypes" required />
      </UFormGroup>
      <UFormGroup label="เป็นสมาชิกตั้งแต่" help="วันที่เริ่มเป็นสมาชิก">
        <UInput type="date" v-model="applicationDate" required />
      </UFormGroup>
      <UButton type="submit" class="submit-button">Check Eligibility</UButton>
    </form>
    <div v-if="result" class="result-section">
      <ul style="list-style-type: disc; padding-left: 20px; text-align: left;">
        <li><strong>สิทธิ์ขอสินเชื่อ (เบื้องต้น):</strong> {{ isEligible ? 'มีสิทธิ์' : 'ไม่มีสิทธิ์' }}</li>
        <li><strong>เป็นสมาชิกมาแล้ว (ปี):</strong> {{ memberYears }} years</li>
        <li v-if="isEligible"><strong>วงเงินสินเชื่อ (บาท - เบื้องต้น):</strong> {{ formattedLoanAmount }}</li>
      </ul>
    </div>
  </UContainer>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

const salary = ref<number>(0);
const additionalIncome = ref<number>(0);
const expenses = ref<number>(0);
const memberTypes = ['ข้าราชการ', 'ลูกจ้างประจำ', 'พนักงานสัญญาจ้าง'];
const memberType = ref<string>(memberTypes[0]);
const applicationDate = ref<string>('');
const result = ref<boolean | null>(null);
const memberYears = ref<number>(0);
const isEligible = ref<boolean>(false);
const eligibleLoanAmount = ref<number>(0);

const formattedLoanAmount = computed(() => {
  return eligibleLoanAmount.value.toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 });
});

function checkEligibility() {
  const netAvailable = salary.value + additionalIncome.value - expenses.value;
  const netAvailablePayment = netAvailable * 0.8;
  const applicationDateValue = new Date(applicationDate.value);
  const currentDate = new Date();
  const memberYearsValue = currentDate.getFullYear() - applicationDateValue.getFullYear();
  memberYears.value = memberYearsValue;
  if (memberYearsValue > 5) {
    isEligible.value = true;
    eligibleLoanAmount.value = netAvailablePayment * 80;
  } else {
    isEligible.value = false;
    eligibleLoanAmount.value = 0;
  }
  result.value = true;
}
</script>

<style scoped>
.check-loan-container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.title {
  text-align: center;
  margin-bottom: 20px;
  color: #333;
}

.loan-form {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.submit-button {
  align-self: center;
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.submit-button:hover {
  background-color: #0056b3;
}

.result-section {
  margin-top: 20px;
  padding: 15px;
  background-color: #e9ecef;
  border-radius: 4px;
  text-align: center;
}
</style>