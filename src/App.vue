<template>
  <v-app>
    <v-main>
      <v-layout class="rounded rounded-md h-100">
        <v-app-bar class="bg-teal-lighten-1 py-2">
          <v-tooltip text="Фильтры" location="bottom">
            <template v-slot:activator="{ props }">
              <v-btn v-bind="props" icon @click="toggleFilters" class="mx-2">
                <v-icon>{{ filtersVisible ? 'mdi-filter' : 'mdi-filter-menu' }}</v-icon>
              </v-btn>
            </template>
          </v-tooltip>

          <v-row dense class="align-center">
            <v-col cols="12" md="2" class="d-flex justify-end">
              Дата регистрации:
            </v-col>

            <v-col cols="12" md="3" class="mt-6">
              <v-date-input v-model="dateRange.start" label="с" variant="outlined"></v-date-input>
            </v-col>

            <v-col cols="12" md="3" class="mt-6">
              <v-date-input v-model="dateRange.end" label="по" variant="outlined" prepend-icon=""></v-date-input>
            </v-col>

            <v-col cols="12" md="3">
              <v-btn @click="clearDates">Сбросить даты</v-btn>
            </v-col>
          </v-row>
        </v-app-bar>

        <v-navigation-drawer v-if="filtersVisible" class="mt-4">
          <v-expand-transition>
            <div>
              <div class="d-flex justify-space-between align-center px-4 pt-4">
                <span>Фильтры</span>
                <v-tooltip text="Сбросить фильтры" location="bottom">
                  <template v-slot:activator="{ props }">
                    <v-btn v-bind="props" icon @click="clearFilters">
                      <v-icon>mdi-eraser</v-icon>
                    </v-btn>
                  </template>
                </v-tooltip>
              </div>

              <v-card-text>
                <v-text-field v-model="filter.ids" label="IDS" />
                <v-text-field v-model="filter.patient" label="Фамилия пациента" />
                <v-select v-model="filter.customer" :items="customers" label="Заказчик"
                  clearable />
              </v-card-text>
            </div>
          </v-expand-transition>
        </v-navigation-drawer>

        <v-main class="d-flex justify-center mt-4">
          <v-card class="w-100">
            <v-table class="elevation-1">
              <thead>
                <tr>
                  <th v-for="header in this.headers">
                    {{ header.title }}
                  </th>
                </tr>
              </thead>
              <tbody>
                <tr
                  v-for="item in filteredItems"
                  :key="item.ids"
                  :class="coloredByCustomer(item.customer)"
                >
                  <td>{{ item.ids }}</td>
                  <td>{{ item.formattedRegDate }}</td>
                  <td>{{ item.cardNumber }}</td>
                  <td>{{ item.patient }}</td>
                  <td>{{ item.customer }}</td>
                </tr>
              </tbody>
            </v-table>
          </v-card>
        </v-main>
      </v-layout>
    </v-main>
  </v-app>
</template>

<script>
import { useDate } from 'vuetify'

export default {
  table: '#table',
  data() {
    return {
      filtersVisible: true,
      dateRange: {
        start: null,
        end: null,
      },
      filter: {
        ids: '',
        patient: '',
        customer: null,
      },
      customers: ['Заказчик 1', 'Заказчик 2', 'Заказчик 3', 'Заказчик 4', 'Заказчик 5'],
      headers: [
        { title: 'IDS', value: 'ids' },
        { title: 'Дата регистрации', value: 'formattedRegDate' },
        { title: 'Номер карты', value: 'cardNumber' },
        { title: 'Пациент', value: 'patient' },
        { title: 'Заказчик', value: 'customer' },
      ],
      items: [
        { ids: '1123456', regDate: '2025-02-01T01:24:00.000Z', formattedRegDate: '', cardNumber: '16', customer: 'Заказчик 1', patient: 'Иванов И.И.' },
        { ids: '2654321', regDate: '2025-02-05T01:24:00.000Z', formattedRegDate: '', cardNumber: '61', customer: 'Заказчик 2', patient: 'Петров П.П.' },
        { ids: '3789012', regDate: '2025-02-10T02:23:00.000Z', formattedRegDate: '', cardNumber: '72', customer: 'Заказчик 3', patient: 'Сидоров С.С.' },
        { ids: '4345678', regDate: '2025-02-12T12:23:00.000Z', formattedRegDate: '', cardNumber: '38', customer: 'Заказчик 1', patient: 'Кузнецов К.К.' },
        { ids: '5901234', regDate: '2025-03-01T17:53:00.000Z', formattedRegDate: '', cardNumber: '94', customer: 'Заказчик 4', patient: 'Николаев Н.Н.' },
        { ids: '6111222', regDate: '2025-03-05T21:54:00.000Z', formattedRegDate: '', cardNumber: '12', customer: 'Заказчик 5', patient: 'Федоров Ф.Ф .' },
        { ids: '7333444', regDate: '2025-04-01T21:54:00.000Z', formattedRegDate: '', cardNumber: '34', customer: 'Заказчик 3', patient: 'Михайлов М.М.' },
        { ids: '8555666', regDate: '2025-04-10T11:44:00.000Z', formattedRegDate: '', cardNumber: '56', customer: 'Заказчик 5', patient: 'Васильев В.В.' },
        { ids: '9525666', regDate: '2025-05-10T11:44:00.000Z', formattedRegDate: '', cardNumber: '56', customer: 'Заказчик 1', patient: 'Лебедев А.А.' },
        { ids: '8525667', regDate: '2025-04-11T09:30:00.000Z', formattedRegDate: '', cardNumber: '57', customer: 'Заказчик 2', patient: 'Крылов Б.Б.' },
        { ids: '4525668', regDate: '2025-04-12T14:15:00.000Z', formattedRegDate: '', cardNumber: '58', customer: 'Заказчик 3', patient: 'Орлов В.В.' },
        { ids: '7525669', regDate: '2025-05-13T10:00:00.000Z', formattedRegDate: '', cardNumber: '59', customer: 'Заказчик 4', patient: 'Горбунов Г.Г.' },
        { ids: '8525670', regDate: '2025-04-14T16:45:00.000Z', formattedRegDate: '', cardNumber: '60', customer: 'Заказчик 5', patient: 'Зайцева Д.Д.' },
        { ids: '3525671', regDate: '2025-06-15T08:20:00.000Z', formattedRegDate: '', cardNumber: '61', customer: 'Заказчик 1', patient: 'Козлов Е.Е.' },
        { ids: '8525672', regDate: '2025-04-16T13:10:00.000Z', formattedRegDate: '', cardNumber: '62', customer: 'Заказчик 2', patient: 'Мельников Ж.Ж.' },
        { ids: '5525673', regDate: '2025-06-17T11:55:00.000Z', formattedRegDate: '', cardNumber: '63', customer: 'Заказчик 3', patient: 'Никитин З.З.' },
        { ids: '2525674', regDate: '2025-05-18T15:40:00.000Z', formattedRegDate: '', cardNumber: '64', customer: 'Заказчик 4', patient: 'Павлова И.И.' },
        { ids: '8525675', regDate: '2025-04-19T12:25:00.000Z', formattedRegDate: '', cardNumber: '65', customer: 'Заказчик 5', patient: 'Романов К.К.' }
      ],
    };
  },

  computed: {
    filteredItems() {
      return this.items.filter(item => {
        const withinDateRange =
          (!this.dateRange.start || new Date(item.regDate) >= new Date(this.dateRange.start)) &&
          (!this.dateRange.end || new Date(item.regDate) <= new Date(this.dateRange.end));

        const matchesIds = item.ids.toString().includes(this.filter.ids);
        const matchesPatient = item.patient.toLowerCase().includes(this.filter.patient.toLowerCase());
        const matchesCustomer = !this.filter.customer || item.customer === this.filter.customer;

        return withinDateRange && matchesIds && matchesPatient && matchesCustomer;
      });
    },
  },

  methods: {
    toggleFilters() {
      this.filtersVisible = !this.filtersVisible;
    },

    clearFilters() {
      this.filter.ids = '';
      this.filter.patient = '';
      this.filter.customer = null;
    },

    clearDates() {
      this.dateRange.start = null;
      this.dateRange.end = null;
    },
    coloredByCustomer(customer) {
      switch (customer) {
        case 'Заказчик 1':
          return 'bg-yellow-lighten-4'
        case 'Заказчик 3':
          return 'bg-purple-lighten-4'
        case 'Заказчик 5':
          return 'bg-red-lighten-4'
        default:
          return 'bg-white'
      }
    }
  },

  mounted() {
    this.items.forEach(item => {
      item.formattedRegDate = useDate().format(item.regDate, 'keyboardDateTime');
    });
  }
};
</script>

<style>
</style>
