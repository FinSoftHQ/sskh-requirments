<template>
  <UContainer>
    <h1>ตรวจสอบสิทธิ์การขอสินเชื่อ</h1>
    <form @submit.prevent="checkEligibility">
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
      <UButton type="submit">Check Eligibility</UButton>
    </form>
    <div v-if="result">
      <p>Member for: {{ memberYears }} years</p>
      <p>Eligible for loan: {{ isEligible ? 'Yes' : 'No' }}</p>
      <p v-if="isEligible">Eligible loan amount: {{ eligibleLoanAmount }}</p>
    </div>
  </UContainer>
</template>

<script setup lang="ts">
import { ref } from 'vue';

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