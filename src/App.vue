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
    <button @click="exportToExcel">Экспорт в Excel</button>
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
      }
    };
  },
  methods: {
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
</style>
