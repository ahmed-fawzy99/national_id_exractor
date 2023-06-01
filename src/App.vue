<script setup>

import IconDocumentation from "@/components/icons/IconDocumentation.vue";
import {computed, onMounted, reactive, ref, watch} from "vue"

const userInput = ref('29412010135971');
const focusNatId = ref(null);
const localeOptions = {year: 'numeric', month: 'long', day: 'numeric'};


onMounted(() => {
  // userInput.value = localStorage.getItem('storedData');
  focusNatId.value.focus();
})

function extractDateOfBirthAndInfo(nationalId) {
  const centuryFlag = true;
  const centuryNumber = parseInt(nationalId.charAt(0));
  const centuryOffset = (centuryNumber - 1) * 100;
  let yearOfBirth = 1800 + centuryOffset;

  if (nationalId.length >= 2) {
    nationalId.length == 2 ? nationalId += '0' : '';
    yearOfBirth += parseInt(nationalId.substr(1, 2));
  }
  let result = {
    year: yearOfBirth,
    month: '',
    day: '',
    date: '',
    date_age: '',
    state: '',
    isMale: null,
    centuryFlag: true,
  };
  if (centuryNumber !== 2 && centuryNumber !== 3){
    result.year = 'صيغة خاطئة';
    result.centuryFlag= false;
    return result;
  }
  if (nationalId.length >= 5) {
    const monthOfBirth = parseInt(nationalId.substr(3, 2));
    result.month = monthOfBirth;
  }
  if (nationalId.length >= 7) {
    const dayOfBirth = parseInt(nationalId.substr(5, 2));
    result.day = dayOfBirth;
  }
  if (nationalId.length >= 9) {
    const stateCode = nationalId.substr(7, 2);
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
    result.state = states[stateCode];
  }
  if (nationalId.length >= 13) {
    const genderNumber = parseInt(nationalId.charAt(12));
    result.isMale = genderNumber % 2 !== 0;
  }
  if (nationalId.length >= 7) {
    result.date = new Date(result.year, result.month - 1, result.day).toLocaleDateString('ar-EG', localeOptions);
    result.date_age = new Date(result.year, result.month - 1, result.day);
  }
  return result;
}

function write() {
  localStorage.setItem('storedData', userInput.value);
}

let numberChecker = reactive(0);

function age(birthday){
  const ageDifMs = Date.now() - birthday;
  const ageDate = new Date(ageDifMs);
  return Math.abs(ageDate.getUTCFullYear() - 1970);
}

watch(userInput, () => {
  //write();
  let length = userInput.value.toString().length;
  if (length < 1) {
    numberChecker = 0;
  } else if (length >= 1 && length < 14) {
    numberChecker = 1;
  } else if (length == 14) {
    numberChecker = 2;
  } else {
    numberChecker = 3;
  }
});


</script>

<template>
  <div class="flex items-center justify-center h-screen">
    <div class="w-full max-w-md mx-auto p-4">
      <div class="bg-white rounded-lg p-8 shadow">

        <div class="flex justify-between items-center pb-6 border-b-2">
          <h1 class="font-bold">اكشف عن رقم قومي</h1>
          <icon-documentation></icon-documentation>
        </div>

        <div class="mt-6 pb-8 border-b-2">
          <label for="id" class="block mb-2  text-sm font-medium text-gray-900"> الرقم القومي (14
            رقم)</label>
          <input type="number" v-model="userInput" ref="focusNatId"
                 class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 "
                 placeholder="29805140124832" maxlength="14" required
          />
        </div>

        <div class="text-center pt-4">
          <p v-if="numberChecker" class="pb-2">
            <div v-if="numberChecker == 1" class="text-orange-500"> رقم غير مكتمل</div>
            <div v-else-if="numberChecker == 2" >
              <div v-if="extractDateOfBirthAndInfo(userInput.toString()).centuryFlag" class="text-green-500">
              رقم صحيح
              </div>
              <div v-else class="text-red-500">
                صيغة خاطئة
              </div>
            </div>
            <div v-else class="text-red-500">صيغة خاطئة</div>
          </p>

          <div class="flex flex-col">
            <div class="overflow-x-auto sm:-mx-6 lg:-mx-8">
              <div class="inline-block min-w-full py-2 sm:px-6 lg:px-8">
                <div class="overflow-hidden">
                  <table class="min-w-full table-fixed text-right text-sm font-light">
                    <tbody>
                    <tr class="bg-neutral-100">
                      <td class="whitespace-nowrap px-6 py-4 font-semibold text-center w-1/2">تاريخ الميلاد</td>
                      <td class=" px-6 py-4 text-center w-1/2">
                        <template v-if="userInput.toString().length === 0">
                          -
                        </template>
                        <template v-else-if="userInput.toString().length < 7">
                          {{ `${extractDateOfBirthAndInfo(userInput.toString()).month ?? '60'} ${extractDateOfBirthAndInfo(userInput.toString()).year}` }}
                        </template>
                        <template v-else>
                          <div v-if="extractDateOfBirthAndInfo(userInput.toString()).centuryFlag">
                          {{ `${extractDateOfBirthAndInfo(userInput.toString()).date} - ${age(extractDateOfBirthAndInfo(userInput.toString()).date_age)} سنة` }}
                          </div>
                          <div v-else> صيغة خاطئة </div>
                        </template>
                      </td>
                    </tr>

                    <tr class="bg-white">
                      <td class="whitespace-nowrap px-6 py-4  font-semibold text-center w-1/2">محل الميلاد</td>
                      <td class="whitespace-nowrap px-6 py-4  text-center">
                        <div v-if="userInput.toString().length >= 9 && extractDateOfBirthAndInfo(userInput.toString()).centuryFlag">
                          {{ `${extractDateOfBirthAndInfo(userInput.toString()).state} ` }}
                        </div>
                        <div v-else>
                          -
                        </div>
                      </td>
                    </tr>
                    <tr class="bg-neutral-100 w-1/2">
                      <td class="whitespace-nowrap px-6 py-4  font-semibold text-center w-1/2">النوع</td>
                      <td class="whitespace-nowrap px-6 py-4  text-center">
                        <div v-if="userInput.toString().length >= 13 && userInput.toString().length <= 14 && extractDateOfBirthAndInfo(userInput.toString()).centuryFlag">
                          {{ `${extractDateOfBirthAndInfo(userInput.toString()).isMale ? 'ذكر' : 'أنثى'} ` }}
                        </div>
                        <div v-else>
                          -
                        </div>
                      </td>
                    </tr>
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          </div>

        </div>
      </div>
      <nav class="text-center mt-5 align-middle">
        <a href="https://github.com/ahmed-fawzy99/national_id_exractor" target="_blank" rel="noopener noreferrer" class="flex items-center justify-center ">
          <span class="mr-1.5 text-sm mt-0.5">Ahmed Fawzy</span>
          <svg viewBox="0 0 24 24" aria-hidden="true" class="h-6 w-6 fill-slate-900 mr-2">
            <path fill-rule="evenodd" clip-rule="evenodd" d="M12 2C6.477 2 2 6.463 2 11.97c0 4.404 2.865 8.14 6.839 9.458.5.092.682-.216.682-.48 0-.236-.008-.864-.013-1.695-2.782.602-3.369-1.337-3.369-1.337-.454-1.151-1.11-1.458-1.11-1.458-.908-.618.069-.606.069-.606 1.003.07 1.531 1.027 1.531 1.027.892 1.524 2.341 1.084 2.91.828.092-.643.35-1.083.636-1.332-2.22-.251-4.555-1.107-4.555-4.927 0-1.088.39-1.979 1.029-2.675-.103-.252-.446-1.266.098-2.638 0 0 .84-.268 2.75 1.022A9.607 9.607 0 0 1 12 6.82c.85.004 1.705.114 2.504.336 1.909-1.29 2.747-1.022 2.747-1.022.546 1.372.202 2.386.1 2.638.64.696 1.028 1.587 1.028 2.675 0 3.83-2.339 4.673-4.566 4.92.359.307.678.915.678 1.846 0 1.332-.012 2.407-.012 2.734 0 .267.18.577.688.48 3.97-1.32 6.833-5.054 6.833-9.458C22 6.463 17.522 2 12 2Z"></path>
          </svg>
        </a>
      </nav>


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
