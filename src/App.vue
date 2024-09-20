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
        name: "Кузов",
        price: 12000,
        quantity: 0,
        subparts: [
          {
            name: "Двери",
            price: 11000,
            quantity: 0,
            subparts: [
              {
                name: "Замок",
                price: 5000,
                quantity: 0,
                subparts: []
              },
              {
                name: "Ручки",
                price: 6000,
                quantity: 0,
                subparts: []
              }
            ]
          },
          {
            name: "Двигатель",
            price: 12000,
            quantity: 0,
            subparts: [
              {
                name: "Поршни",
                price: 10000,
                quantity: 0,
                subparts: []
              },
              {
                name: "Кольца",
                price: 2000,
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
