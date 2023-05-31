<script setup>

import IconDocumentation from "@/components/icons/IconDocumentation.vue";
import {computed, onMounted, reactive, ref, watch} from "vue"

const userInput = ref();
const focusNatId = ref(null);


onMounted(() => {
  userInput.value = localStorage.getItem('storedData');
  focusNatId.value.focus();
})
function extractDateOfBirthAndInfo(nationalId) {
  const centuryNumber = parseInt(nationalId.charAt(0));
  const centuryOffset = (centuryNumber - 1) * 100;
  const yearOfBirth = 1800 + centuryOffset + parseInt(nationalId.substr(1, 2));
  const monthOfBirth = parseInt(nationalId.substr(3, 2));
  const dayOfBirth = parseInt(nationalId.substr(5, 2));
  const stateCode = nationalId.substr(7, 2); // State code

  const states = {
    '01': 'القاهرة',
    '02': 'الإسكندرية',
    '03': 'بورسعيد',
    '04': 'السويس',
    '11': 'دمياط',
    '12': 'الدقهلية',
    '13': 'الشرقية',
    '14': 'القليوبية',
    '15': 'كفر الشيخ',
    '16': 'الغربية',
    '17': 'المنوفية',
    '18': 'البحيرة',
    '19': 'الإسماعيلية',
    '21': 'الجيزة',
    '22': 'بني سويف',
    '23': 'الفيوم',
    '24': 'المنيا',
    '25': 'أسيوط',
    '26': 'سوهاج',
    '27': 'قنا',
    '28': 'أسوان',
    '29': 'الأقصر',
    '31': 'البحر الأحمر',
    '32': 'الوادي الجديد',
    '33': 'مطروح',
    '34': 'شمال سيناء',
    '35': 'جنوب سيناء',
    '88': 'خارج الجمهورية'
  };
  const state = states[stateCode]; // State name
  const genderNumber = parseInt(nationalId.charAt(12));
  const isMale = genderNumber % 2 !== 0;
  const options = {year: 'numeric', month: 'long', day: 'numeric' };
  const dateOfBirth = new Date(yearOfBirth, monthOfBirth - 1, dayOfBirth).toLocaleDateString('ar-EG', options);

  return {
    year: yearOfBirth,
    month: monthOfBirth,
    day: dayOfBirth,
    date: dateOfBirth,
    state: state,
    isMale: isMale,
  };
}
function write(){
  localStorage.setItem('storedData', userInput.value);
}

const DOB = reactive([0, '', '', '']);


watch(userInput, ()=>{
  write();
  let length = userInput.value.toString().length;
  if (length < 1){
    DOB[0] = 0;
  }
  else if (length >= 1 && length < 14){
    DOB[0] = 1;
  }
  else if (length == 14){
    DOB[0] = 2;
  }
  else {
    DOB[0] = 3;
  }
});


</script>

<template>
  <div class="flex items-center justify-center h-screen">
    <div class="w-full max-w-sm mx-auto p-4">
      <div class="bg-white rounded-lg p-8 shadow">

        <div class="flex justify-between items-center pb-6 border-b-2">
          <h1 class="font-bold">اكشف عن رقم قومي</h1>
          <icon-documentation></icon-documentation>
        </div>

        <div class="mt-6 pb-8 border-b-2">
          <label for="id" class="block mb-2  text-sm font-medium text-gray-900 dark:text-white"> الرقم القومي (14 رقم)</label>
          <input type="number" v-model="userInput" ref="focusNatId"
                 class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
                 placeholder="29805140124832" maxlength="14" required
          />
        </div>

        <div class="text-center pt-4">
          <p v-if="DOB[0]" class="pb-2" >
          <div v-if="DOB[0] == 1" class="text-orange-500"> رقم غير مكتمل  </div>
            <div v-else-if="DOB[0] == 2" class="text-green-500">رقم صحيح</div>
            <div v-else class="text-red-500">رقم خاطيء</div>
          </p>

          <div class="flex flex-col">
            <div class="overflow-x-auto sm:-mx-6 lg:-mx-8">
              <div class="inline-block min-w-full py-2 sm:px-6 lg:px-8">
                <div class="overflow-hidden">
                  <table class="min-w-full table-fixed text-right text-sm font-light">
                    <tbody>
                      <tr class="bg-neutral-100 dark:border-neutral-500 dark:bg-neutral-700">
                        <td class="whitespace-nowrap px-6 py-4 font-semibold text-center">تاريخ الميلاد</td>
                        <td class="whitespace-nowrap px-6 py-4  text-center">
                          {{ extractDateOfBirthAndInfo('29904260103712').date }} - 24 سنة
                        </td>
                      </tr>
                      <tr class="bg-white dark:border-neutral-500 dark:bg-neutral-700">
                        <td class="whitespace-nowrap px-6 py-4  font-semibold text-center">النوع</td>
                        <td class="whitespace-nowrap px-6 py-4  text-center">ذكر</td>
                      </tr>
                      <tr class="bg-neutral-100 dark:border-neutral-500 dark:bg-neutral-700">
                        <td class="whitespace-nowrap px-6 py-4  font-semibold text-center">محل الميلاد</td>
                        <td class="whitespace-nowrap px-6 py-4  text-center">القاهرة</td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          </div>

        </div>
      </div>
    </div>
  </div>

</template>

<style scoped>
/* Hide Spin arrows on input type number */
input[type="number"]::-webkit-outer-spin-button,
input[type="number"]::-webkit-inner-spin-button {
  /* display: none; <- Crashes Chrome on hover */
  -webkit-appearance: none;

  margin: 0; /* <-- Apparently some margin are still there even though it's hidden */
}
input[type="number"] {
  -moz-appearance: textfield;
}
</style>
