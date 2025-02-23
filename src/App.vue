<template>
  <v-app>
    <v-main>
      <v-layout class="rounded rounded-md h-100">
        <v-app-bar class="bg-teal-lighten-1 py-2">
          <v-btn icon @click="toggleFilters">
            <v-icon>{{ filtersVisible ? 'mdi-chevron-up' : 'mdi-chevron-down' }}</v-icon>
          </v-btn>

          <v-row dense class="align-center">
            <v-col cols="12" md="2">
              <v-span>Дата регистрации:</v-span>
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
              <v-div class="d-flex justify-space-between align-center px-4 pt-4">
                <v-span>Фильтры</v-span>
                <v-btn icon @click="clearFilters">
                  <v-icon>mdi-eraser</v-icon>
                </v-btn>
              </v-div>

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
        { ids: '1123456', regDate: '2025-02-01T01:24:00.000Z', formattedRegDate: '', cardNumber: '16', patient: 'Иванов И.И.', customer: 'Заказчик 1' },
        { ids: '2654321', regDate: '2025-02-05T01:24:00.000Z', formattedRegDate: '', cardNumber: '61', patient: 'Петров П.П.', customer: 'Заказчик 2' },
        { ids: '3789012', regDate: '2025-02-10T02:23:00.000Z', formattedRegDate: '', cardNumber: '72', patient: 'Сидоров С.С.', customer: 'Заказчик 3' },
        { ids: '4345678', regDate: '2025-02-12T12:23:00.000Z', formattedRegDate: '', cardNumber: '38', patient: 'Кузнецов К.К.', customer: 'Заказчик 1'},
        { ids: '5901234', regDate: '2025-03-01T17:53:00.000Z', formattedRegDate: '', cardNumber: '94', patient: 'Николаев Н.Н.', customer: 'Заказчик 4'},
        { ids: '6111222', regDate: '2025-03-05T21:54:00.000Z', formattedRegDate: '', cardNumber: '12', patient: 'Федоров Ф.Ф .', customer: 'Заказчик 5'},
        { ids: '7333444', regDate: '2025-04-01T21:54:00.000Z', formattedRegDate: '', cardNumber: '34', patient: 'Михайлов М.М.', customer: 'Заказчик 3'},
        { ids: '8555666', regDate: '2025-04-10T11:44:00.000Z', formattedRegDate: '', cardNumber: '56', patient: 'Васильев В.В.', customer: 'Заказчик 5'}
      ],
    };
  },

  computed: {
    filteredItems() {
      if (
        this.filter.ids === '' &&
        this.filter.patient === '' &&
        this.filter.customer === null &&
        this.dateRange.start === null &&
        this.dateRange.end === null
      ) {
        return this.items.filter(item => {
          item.ids === 0
        });
      } else {
        return this.items.filter(item => {
          const withinDateRange =
            (!this.dateRange.start || new Date(item.regDate) >= new Date(this.dateRange.start)) &&
            (!this.dateRange.end || new Date(item.regDate) <= new Date(this.dateRange.end));

          const matchesIds = item.ids.toString().includes(this.filter.ids);
          const matchesPatient = item.patient.toLowerCase().includes(this.filter.patient.toLowerCase());
          const matchesCustomer = !this.filter.customer || item.customer === this.filter.customer;

          return withinDateRange && matchesIds && matchesPatient && matchesCustomer;
        });
      }
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
      if (customer === 'Заказчик 1') {
        return 'bg-yellow-lighten-4';
      } else if (customer === 'Заказчик 3') {
        return 'bg-purple-lighten-4';
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