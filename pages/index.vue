<template>
  <v-container>
    <v-data-table :headers="computedTableHeaders" :items="filteredItems" :search="search" item-key="id"
      class="elevation-1">

      <template v-slot:top>
        <v-toolbar color="secondary" dark>
          <v-toolbar-title>Employees</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-text-field v-model="search" label="Search" variant="solo-filled" hide-details clearable
            dense></v-text-field>
          <v-btn @click="toggleModal">
            <v-icon>mdi-table</v-icon>
          </v-btn>
        </v-toolbar>
      </template>

      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="editEmployee(item)">mdi-pencil</v-icon>
        <v-icon small color="red" @click="deleteEmployee(item)">mdi-delete</v-icon>
      </template>
    </v-data-table>

    <v-dialog v-model="dialog" max-width="400">
      <v-card>
        <v-card-title>
          <span class="headline">Columns</span>
        </v-card-title>
        <v-card-text>
          <div v-for="header in allHeaders" :key="header.value" class="compact-checkbox-wrapper">
            <label>
              <input type="checkbox" v-model="header.show" class="checkbox mb-3">&nbsp;{{ header.title }}
            </label>
          </div>
        </v-card-text>
        <v-card-actions>
          <v-btn text @click="applyVisibleHeader">Apply</v-btn>
          <v-btn text @click="dialog = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

onMounted(() => {
  applyVisibleHeader();
});

const search = ref('')
const dialog = ref(false)
const items = ref([
  {
    id: 1,
    name: "John Doe",
    position: "Software Engineer",
    department: "Development",
    email: "johndoe@example.com",
    phone: "+1-555-1234",
    hireDate: "2020-03-15",
  },
  {
    id: 2,
    name: "Jane Smith",
    position: "Project Manager",
    department: "Management",
    email: "janesmith@example.com",
    phone: "+1-555-5678",
    hireDate: "2019-07-23",
  },
  {
    id: 3,
    name: "Alice Johnson",
    position: "UX Designer",
    department: "Design",
    email: "alicejohnson@example.com",
    phone: "+1-555-8765",
    hireDate: "2021-02-10",
  },
  {
    id: 4,
    name: "Bob Brown",
    position: "Data Analyst",
    department: "Analytics",
    email: "bobbrown@example.com",
    phone: "+1-555-4321",
    hireDate: "2018-09-08",
  },
  {
    id: 5,
    name: "Chris Evans",
    position: "DevOps Engineer",
    department: "Operations",
    email: "chrisevans@example.com",
    phone: "+1-555-3456",
    hireDate: "2022-01-12",
  },
  {
    id: 6,
    name: "Diana Green",
    position: "HR Specialist",
    department: "Human Resources",
    email: "dianagreen@example.com",
    phone: "+1-555-7890",
    hireDate: "2017-04-28",
  },
  {
    id: 7,
    name: "Ethan White",
    position: "Sales Manager",
    department: "Sales",
    email: "ethanwhite@example.com",
    phone: "+1-555-1122",
    hireDate: "2019-11-05",
  },
  {
    id: 8,
    name: "Fiona Black",
    position: "Marketing Specialist",
    department: "Marketing",
    email: "fionablack@example.com",
    phone: "+1-555-3344",
    hireDate: "2020-05-19",
  },
  {
    id: 9,
    name: "George Martin",
    position: "Database Administrator",
    department: "IT",
    email: "georgemartin@example.com",
    phone: "+1-555-5566",
    hireDate: "2021-06-21",
  },
  {
    id: 10,
    name: "Hannah Wilson",
    position: "Content Writer",
    department: "Content",
    email: "hannahwilson@example.com",
    phone: "+1-555-7788",
    hireDate: "2018-12-12",
  },
  {
    id: 11,
    name: "Isaac King",
    position: "Product Manager",
    department: "Product",
    email: "isaacking@example.com",
    phone: "+1-555-9911",
    hireDate: "2022-08-17",
  },
  {
    id: 12,
    name: "Julia Roberts",
    position: "Quality Assurance",
    department: "QA",
    email: "juliaroberts@example.com",
    phone: "+1-555-2233",
    hireDate: "2019-03-07",
  },
  {
    id: 13,
    name: "Kevin Lee",
    position: "Frontend Developer",
    department: "Development",
    email: "kevinlee@example.com",
    phone: "+1-555-4455",
    hireDate: "2020-10-02",
  },
  {
    id: 14,
    name: "Laura Scott",
    position: "Customer Support",
    department: "Support",
    email: "laurascott@example.com",
    phone: "+1-555-6677",
    hireDate: "2017-01-23",
  },
  {
    id: 15,
    name: "Michael Clark",
    position: "Network Engineer",
    department: "IT",
    email: "michaelclark@example.com",
    phone: "+1-555-8899",
    hireDate: "2021-09-15",
  },
  {
    id: 16,
    name: "Natalie Turner",
    position: "Graphic Designer",
    department: "Design",
    email: "natalieturner@example.com",
    phone: "+1-555-1010",
    hireDate: "2018-02-19",
  },
  {
    id: 17,
    name: "Oliver Young",
    position: "System Administrator",
    department: "Operations",
    email: "oliveryoung@example.com",
    phone: "+1-555-1212",
    hireDate: "2020-11-22",
  },
  {
    id: 18,
    name: "Patricia Adams",
    position: "Business Analyst",
    department: "Business",
    email: "patriciaadams@example.com",
    phone: "+1-555-1414",
    hireDate: "2019-06-14",
  },
  {
    id: 19,
    name: "Richard Harris",
    position: "SEO Specialist",
    department: "Marketing",
    email: "richardharris@example.com",
    phone: "+1-555-1616",
    hireDate: "2021-04-03",
  },
  {
    id: 20,
    name: "Sophia Wright",
    position: "Social Media Manager",
    department: "Marketing",
    email: "sophiawright@example.com",
    phone: "+1-555-1818",
    hireDate: "2017-08-30",
  },
  {
    id: 21,
    name: "Thomas Baker",
    position: "Back-End Developer",
    department: "Development",
    email: "thomasbaker@example.com",
    phone: "+1-555-2020",
    hireDate: "2022-07-05",
  }
]);

const visibleTableHeaders = ref([]);
const allHeaders = ref([
  { title: "Name", key: "name", show: true },
  { title: "Position", key: "position", show: true },
  { title: "Department", key: "department", show: true },
  { title: "Email", key: "email", show: true },
  { title: "Phone", key: "phone", show: true },
  { title: "Hire Date", key: "hireDate", show: true }
]);

const toggleModal = () => dialog.value = !dialog.value;

const applyVisibleHeader = () => {
  visibleTableHeaders.value = allHeaders.value.filter((e) => e.show);
  dialog.value = false;
}

const computedTableHeaders = computed(() => {
  let localHeaders = [];
  localHeaders = visibleTableHeaders.value.map(header => ({ title: header.title, key: header.key }));
  localHeaders.push({ title: 'Actions', key: 'actions', align: 'center', sortable: false });
  return localHeaders;
});

const editEmployee = (item) => {
  console.log('Edit clicked for:', item);
  // Add your edit logic here
};

const deleteEmployee = (item) => {
  console.log('Delete clicked for:', item);
  // Add your delete logic here
};

const filteredItems = computed(() => {
  return items.value.filter(item => {
    return Object.keys(item).some(key => {
      return String(item[key]).toLowerCase().includes(search.value.toLowerCase());
    });
  });
});

</script>

<style scoped>
.checkbox {
  transform: scale(1.2);
  margin-right: 8px;
}
</style>
