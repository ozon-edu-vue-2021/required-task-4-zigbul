<template>
  <form class="form" autocomplete="off">
    <h2 class="title">Личный данные</h2>

    <div class="row">
      <div>
        <label for="surname">Фамилия</label>
        <input id="surname" type="text" v-model="formData.surname" />
      </div>
      <div>
        <label for="name">Имя</label>
        <input id="name" v-model="formData.name" />
      </div>
      <div>
        <label for="patronymic">Отчество</label>
        <input id="patronymic" v-model="formData.patronymic" />
      </div>
    </div>

    <div class="row">
      <div>
        <label for="birth-date">Дата рождения</label>
        <input
          id="birth-date"
          placeholder="дд.мм.ггг"
          v-model="formData.birthDate"
        />
      </div>
    </div>

    <div class="row">
      <div>
        <label for="e-mail">E-mail</label>
        <input id="e-mail" v-model="formData.email" />
      </div>
    </div>

    <h2>Пол</h2>

    <div class="row">
      <div>
        <label>
          <input
            type="radio"
            name="sex"
            value="Мужской"
            v-model="formData.sex"
          />
          Мужской
        </label>
      </div>
      <div>
        <label>
          <input
            type="radio"
            name="sex"
            value="Женский"
            v-model="formData.sex"
          />
          Женский
        </label>
      </div>
    </div>

    <h2>Паспортные данные</h2>

    <div class="row">
      <div
        class="citizenship-selector"
        v-click-outside="hideCitizenshipDropdown"
      >
        <label for="citizenship">Гражданство</label>
        <input
          id="citizenship"
          @focus="isCitizenshipDropdownOpen = true"
          v-model="formData.citizenship"
        />
        <div
          v-if="isCitizenshipDropdownOpen"
          class="citizenship-selector__dropdown"
        >
          <ul v-if="citizenshipFilter.length">
            <li
              v-for="citizenship in citizenshipFilter"
              :key="citizenship.id"
              @click="onCitizenshipClicked(citizenship)"
            >
              {{ citizenship.nationality }}
            </li>
          </ul>
          <div v-else class="empty">Ничего не найдено</div>
        </div>
      </div>
    </div>

    <div
      class="row"
      v-if="formData.citizenship === 'Russia' && formData.citizenship !== ''"
    >
      <div>
        <label for="passport-serial">Серия паспорта</label>
        <input id="passport-serial" v-model="formData.passportSerial" />
      </div>
      <div>
        <label for="passport-number">Номер паспорта</label>
        <input id="passport-number" v-model="formData.passportNumber" />
      </div>
      <div>
        <label for="passport-date">Дата выдачи</label>
        <input id="passport-date" v-model="formData.passportDate" />
      </div>
    </div>

    <div
      class="row"
      v-if="formData.citizenship !== 'Russia' && formData.citizenship !== ''"
    >
      <div>
        <label for="latin-surname">Фамилия на латинице</label>
        <input id="latin-surname" v-model="formData.latinSurname" />
      </div>
      <div>
        <label for="latin-name">Имя на латинице</label>
        <input id="latin-name" v-model="formData.latinName" />
      </div>
    </div>

    <div
      class="row"
      v-if="formData.citizenship !== 'Russia' && formData.citizenship !== ''"
    >
      <div>
        <label for="passport-number">Номер паспорта</label>
        <input id="passport-number" v-model="formData.passportNumber" />
      </div>
      <div>
        <label for="passport-country">Страна выдачи</label>
        <input id="passport-country" v-model="formData.passportCountry" />
      </div>
      <div
        class="passport-type-selector"
        v-click-outside="hidePassportTypeDropDown"
      >
        <label for="passport-type">Тип паспорта</label>
        <input
          id="passport-type"
          @focus="isPassportTypeDropdownOpen = true"
          v-model="formData.passportType"
        />

        <div
          v-if="isPassportTypeDropdownOpen"
          class="passport-type-selector__dropdown"
        >
          <ul v-if="allPassportTypes.length">
            <li
              v-for="passportType in allPassportTypes"
              :key="passportType.id"
              @click="onPassportTypeClicked(passportType)"
            >
              {{ passportType.type }}
            </li>
          </ul>
          <div v-else class="empty">Ничего не найдено</div>
        </div>
      </div>
    </div>

    <h2>Меняли ли фамилия или имя?</h2>

    <div class="row">
      <div>
        <label>
          <input
            type="radio"
            name="name-change"
            value="false"
            v-model="formData.nameChange"
          />
          Нет
        </label>
      </div>
      <div>
        <label>
          <input
            type="radio"
            name="name-change"
            value="true"
            v-model="formData.nameChange"
          />
          Да
        </label>
      </div>
    </div>

    <div class="row" v-if="formData.nameChange === 'true'">
      <div>
        <label for="prev-surname">Предыдущая фамилия</label>
        <input id="prev-surname" v-model="formData.prevSurname" />
      </div>
      <div>
        <label for="prev-name">Предыдущее имя</label>
        <input id="prev-name" v-model="formData.prevName" />
      </div>
    </div>

    <button @click.prevent="log">Отправить</button>
  </form>
</template>

<script>
import ClickOutside from "vue-click-outside";
import citizenships from "@/assets/data/citizenships.json";
import passportTypes from "@/assets/data/passport-types.json";
// import { debounce } from '../helpers.js';

export default {
  directives: {
    ClickOutside,
  },
  data() {
    return {
      formData: {
        surname: "",
        name: "",
        patronymic: "",
        birthDate: "",
        email: "",
        sex: "Мужской",
        citizenship: "",
        passportSerial: "",
        passportNumber: "",
        passportDate: "",
        latinSurname: "",
        latinName: "",
        passportCountry: "",
        passportType: "",
        nameChange: "false",
        prevSurname: "",
        prevName: "",
      },
      isCitizenshipDropdownOpen: false,
      isPassportTypeDropdownOpen: false,
      allCitizenships: citizenships,
      allPassportTypes: passportTypes,
    };
  },
  methods: {
    hideCitizenshipDropdown() {
      this.isCitizenshipDropdownOpen = false;
    },
    hidePassportTypeDropDown() {
      this.isPassportTypeDropdownOpen = false;
    },
    onCitizenshipClicked(selectedCitizenship) {
      this.formData.citizenship = selectedCitizenship.nationality;
      this.hideCitizenshipDropdown();
    },
    onPassportTypeClicked(selectedType) {
      this.formData.passportType = selectedType.type;
      this.hidePassportTypeDropDown();
    },
    log() {
      console.log(JSON.stringify(this.formData));
      this.formData = {
        surname: "",
        name: "",
        patronymic: "",
        birthDate: "",
        email: "",
        sex: "Мужской",
        citizenship: "",
        passportSerial: "",
        passportNumber: "",
        passportDate: "",
        latinSurname: "",
        latinName: "",
        passportCountry: "",
        passportType: "",
        nameChange: "false",
        prevSurname: "",
        prevName: "",
      };
    },
    onValueChange(e) {
      this.formData.citizenship = e.target.value;
    },
  },
  computed: {
    citizenshipFilter() {
      if (this.citizenship === "") return this.allCitizenships;

      return this.allCitizenships.filter((city) => {
        if (city.nationality.includes(this.formData.citizenship)) {
          return city;
        }
      });
    },
  },
};
</script>

<style scoped>
.form {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.row {
  display: flex;
}

.row div {
  display: flex;
  flex-direction: column;
}

.citizenship-selector__dropdown,
.passport-type-selector__dropdown {
  max-height: 150px;
  overflow-y: scroll;
}
</style>
