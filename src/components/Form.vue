<template>
  <form class="form" autocomplete="off">
    <h2 class="title">Личный данные</h2>

    <div class="row">
      <div>
        <label for="surname">Фамилия</label>
        <input
          :class="this.errors.surname && 'red'"
          id="surname"
          type="text"
          v-model="formData.surname"
        />
        <span v-if="this.errors.surname">только русские буквы</span>
      </div>
      <div>
        <label for="name">Имя</label>
        <input
          id="name"
          v-model="formData.name"
          :class="this.errors.name && 'red'"
        />
        <span v-if="this.errors.name">только русские буквы</span>
      </div>
      <div>
        <label for="patronymic">Отчество</label>
        <input
          id="patronymic"
          v-model="formData.patronymic"
          :class="this.errors.patronymic && 'red'"
        />
        <span v-if="this.errors.patronymic">только русские буквы</span>
      </div>
    </div>

    <div class="row">
      <div>
        <label for="birth-date">Дата рождения</label>
        <input
          id="birth-date"
          placeholder="дд.мм.ггг"
          v-model="formData.birthDate"
          :class="this.errors.birthDate && 'red'"
        />
        <span v-if="this.errors.birthDate"
          >валидная дата, не позже сегодняшнего числа</span
        >
      </div>
    </div>

    <div class="row">
      <div>
        <label for="e-mail">E-mail</label>
        <input
          id="e-mail"
          v-model="formData.email"
          :class="this.errors.email && 'red'"
        />
        <span v-if="this.errors.email">не валидный email</span>
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
          v-on:input="debounceInput"
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
        <input
          id="passport-serial"
          v-model="formData.passportSerial"
          :class="this.errors.passportSerial && 'red'"
        />
        <span v-if="this.errors.passportSerial">Допустимо 4 цифры</span>
      </div>
      <div>
        <label for="passport-number">Номер паспорта</label>
        <input
          id="passport-number"
          v-model="formData.passportNumber"
          :class="this.errors.passportNumber && 'red'"
        />
        <span v-if="this.errors.passportNumber">Допустимо 6 цифр</span>
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
        <input
          id="latin-surname"
          v-model="formData.latinSurname"
          :class="this.errors.latinSurname && 'red'"
        />
        <span v-if="this.errors.latinSurname">только английские буквы</span>
      </div>
      <div>
        <label for="latin-name">Имя на латинице</label>
        <input
          id="latin-name"
          v-model="formData.latinName"
          :class="this.errors.latinName && 'red'"
        />
        <span v-if="this.errors.latinName">только английские буквы</span>
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
        <input
          id="prev-surname"
          v-model="formData.prevSurname"
          :class="this.errors.prevSurname && 'red'"
        />
        <span v-if="this.errors.prevSurname">только русские буквы</span>
      </div>
      <div>
        <label for="prev-name">Предыдущее имя</label>
        <input
          id="prev-name"
          v-model="formData.prevName"
          :class="this.errors.prevName && 'red'"
        />
        <span v-if="this.errors.prevName">только русские буквы</span>
      </div>
    </div>

    <button @click.prevent="log">Отправить</button>
  </form>
</template>

<script>
import ClickOutside from "vue-click-outside";
import citizenships from "@/assets/data/citizenships.json";
import passportTypes from "@/assets/data/passport-types.json";
// import { debounce } from "../helpers.js";
import _ from "lodash";

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
      filterKey: "",
      errors: {
        surname: false,
        name: false,
        patronymic: false,
        prevName: false,
        prevSurname: false,
        birthDate: false,
        email: false,
        passportSerial: false,
        passportNumber: false,
        latinSurname: false,
        latinName: false,
      },
      validation: true,
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
      if (!this.formData.surname.match(/^[а-яА-ЯёЁ\s]+$/)) {
        this.errors.surname = true;
      } else {
        this.errors.surname = false;
      }

      if (!this.formData.name.match(/^[а-яА-ЯёЁ\s]+$/)) {
        this.errors.name = true;
      } else {
        this.errors.name = false;
      }

      if (!this.formData.patronymic.match(/^[а-яА-ЯёЁ\s]+$/)) {
        this.errors.patronymic = true;
      } else {
        this.errors.patronymic = false;
      }

      if (
        !this.formData.prevSurname.match(/^[а-яА-ЯёЁ\s]+$/) &&
        this.formData.nameChange === "true"
      ) {
        this.errors.prevSurname = true;
      } else {
        this.errors.prevSurname = false;
      }

      if (
        !this.formData.prevName.match(/^[а-яА-ЯёЁ\s]+$/) &&
        this.formData.nameChange === "true"
      ) {
        this.errors.prevName = true;
      } else {
        this.errors.prevName = false;
      }
      if (new Date(this.formData.birthDate).getTime() > Date.now()) {
        this.errors.birthDate = true;
      } else {
        this.errors.birthDate = false;
      }
      if (!this.formData.email.match(/^[\w]{1}[\w-.]*@[\w-]+\.[a-z]{2,4}$/i)) {
        this.errors.email = true;
      } else {
        this.errors.email = false;
      }
      if (!this.formData.passportSerial.match(/\d{4}$/)) {
        this.errors.passportSerial = true;
      } else {
        this.errors.passportSerial = false;
      }
      if (
        this.formData.citizenship === "Russia" &&
        !this.formData.passportNumber.match(/\d{6}$/)
      ) {
        this.errors.passportNumber = true;
      } else {
        this.errors.passportNumber = false;
      }
      if (!this.formData.latinSurname.match(/^[a-zA-Z]+$/)) {
        this.errors.latinSurname = true;
      } else {
        this.errors.latinSurname = false;
      }
      if (!this.formData.latinName.match(/^[a-zA-Z]+$/)) {
        this.errors.latinName = true;
      } else {
        this.errors.latinName = false;
      }

      for (const error in this.errors) {
        if (this.errors[error] === true) {
          return;
        } else {
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
        }
      }
    },
    debounceInput: _.debounce(function () {
      this.filterKey = this.formData.citizenship;
    }, 2000),
  },
  computed: {
    citizenshipFilter() {
      if (this.filterKey === "") return this.allCitizenships;

      return this.allCitizenships.filter((city) => {
        if (city.nationality.includes(this.filterKey)) {
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

.red {
  border: 2px solid red;
}
</style>
