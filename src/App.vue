<template>
  <div id="app">
    <h1>Детали автомобиля</h1>
    <table>
      <thead>
      <tr>
        <th>Деталь</th>
        <th>Цена</th>
        <th>Количество</th>
        <th>Стоимость</th>
        <th>Действия</th>
      </tr>
      </thead>
      <tbody>
      <car-part :part="carParts" />
      </tbody>
    </table>

    <button @click="showModal = true">Добавить новую деталь</button>

    <button @click="exportToExcel">Экспорт в Excel</button>

    <!-- Модальное окно -->
    <div v-if="showModal" class="modal">
      <div class="modal-content">
        <span class="close" @click="showModal = false">&times;</span>
        <h2>Добавить новую деталь</h2>
        <form @submit.prevent="addCarPart">
          <label for="name">Название:</label>
          <input v-model="newPart.name" type="text" id="name" required />

          <label for="price">Цена:</label>
          <input v-model.number="newPart.price" type="number" id="price" required />

          <button type="submit">Добавить</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import * as XLSX from "xlsx";
import CarPart from "./components/CarPart.vue";

export default {
  name: "App",
  components: {
    CarPart
  },
  data() {
    return {
      carParts: {
        name: "1.Кузов",
        price: 12000,
        quantity: 0,
        subparts: [
          {
            name: "1.1.Двери",
            price: 17000,
            quantity: 0,
            subparts: [
              {
                name: "1.2.Замок",
                price: 5000,
                quantity: 0,
                subparts: []
              },
              {
                name: "1.3.Ручки",
                price: 6000,
                quantity: 0,
                subparts: []
              },
              {
                name: "1.4.Стекло",
                price: 4000,
                quantity: 0,
                subparts: []
              },
              {
                name: "1.5.Зеркала",
                price: 2000,
                quantity: 0,
                subparts: []
              }
            ]
          },
          {
            name: "2.Двигатель",
            price: 18000,
            quantity: 0,
            subparts: [
              {
                name: "2.2.Поршни",
                price: 10000,
                quantity: 0,
                subparts: []
              },
              {
                name: "2.3.Кольца",
                price: 2000,
                quantity: 0,
                subparts: []
              },
              {
                name: "2.4.Цилиндр",
                price: 2000,
                quantity: 0,
                subparts: []
              },
              {
                name: "2.5.Свеча",
                price: 2000,
                quantity: 0,
                subparts: []
              },
              {
                name: "2.6.Стартер",
                price: 2000,
                quantity: 0,
                subparts: []
              }
            ]
          },
          {
            name: "3.Шины Японские",
            price: 5000,
            quantity: 0,
            subparts: [
              {
                name: "3.1.Зима",
                price: 2500,
                quantity: 0,
                subparts: []
              },
              {
                name: "3.2.Лето",
                price: 2500,
                quantity: 0,
                subparts: []
              }
            ]
          },
          {
            name: "4.Шины Немецкие",
            price: 5000,
            quantity: 0,
            subparts: [
              {
                name: "4.1.Зима",
                price: 2500,
                quantity: 0,
                subparts: []
              },
              {
                name: "4.2.Лето",
                price: 2500,
                quantity: 0,
                subparts: []
              }
            ]
          }
        ]
      },
      newPart: {
        name: "",
        price: 0,
        quantity: 0,
        subparts: []
      },
      showModal: false
    };
  },
  methods: {
    addCarPart() {
      if (this.newPart.name && this.newPart.price > 0 && this.newPart.quantity >= 0) {
        this.carParts.subparts.push({ ...this.newPart });
        this.newPart.name = "";
        this.newPart.price = 0;
        this.newPart.quantity = 0;
        this.showModal = false; // Закрыть модальное окно после добавления
      }
    },
    exportToExcel() {
      const exportData = this.convertToExport(this.carParts);
      const ws = XLSX.utils.json_to_sheet(exportData);
      const wb = XLSX.utils.book_new();
      XLSX.utils.book_append_sheet(wb, ws, "Car Parts");
      XLSX.writeFile(wb, "car_parts.xlsx");
    },
    convertToExport(part) {
      let data = [];
      const traverse = (part, parentName = "") => {
        data.push({
          Название: parentName ? `${parentName} -> ${part.name}` : part.name,
          Цена: part.price,
          Количество: part.quantity,
          Стоимость: part.price * part.quantity
        });
        part.subparts.forEach((subpart) => traverse(subpart, part.name));
      };
      traverse(part);
      return data;
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  margin-top: 30px;
}
table {
  margin: auto;
  border-collapse: collapse;
  width: 80%;
}
th, td {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;
}
th {
  background-color: #f2f2f2;
}
button {
  margin: 5px;
}
form {
  margin-top: 20px;
}
form label {
  display: block;
  margin: 5px 0;
}
form input {
  margin-bottom: 10px;
  padding: 5px;
}

/* Стили для модального окна */
.modal {
  display: block;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-content {
  background-color: #fff;
  margin: 15% auto;
  padding: 20px;
  border: 1px solid #888;
  width: 40%;
}

.close {
  color: #aaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}
</style>
