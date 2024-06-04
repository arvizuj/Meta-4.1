<template>
    <v-card max-width="1000px" hover title="Activo Tags" color="pink-lighten-5">
      <template v-slot:text>
        <v-text-field 
          v-model="search" 
          label="Search" 
          prepend-inner-icon="mdi-magnify" 
          single-line variant="outlined"
          hide-details>
        </v-text-field>
      </template>
  
      <v-container align="center" justify="center">
        <v-row align="center" justify="center">
          <v-col cols="auto">
            <v-btn 
              elevation="4" 
              density="comfortable"
              color="light-green-lighten-3" @click="showNewDialog = true">
              Nuevo
            </v-btn>
          </v-col>
          <!-- <v-col cols="auto">
            <v-btn 
              elevation="4" 
              density="comfortable" 
              color="amber-lighten-4" @click="showUpdateDialog = true">
              Actualizar
            </v-btn>
          </v-col> -->
          <v-col cols="auto">
            <v-btn 
              elevation="4" 
              density="comfortable" 
              color="red-lighten-3" @click="showDeleteDialog = true">
              Eliminar
            </v-btn>
          </v-col>
        </v-row>
  
      </v-container>
  
      <v-data-table density="compact" 
        :headers="headers" 
        :items="datos" 
        :search="search"
        :sort-by="[{ key: 'id', order: 'asc' }]" 
      ></v-data-table>
  
  
      <v-dialog v-model="showDeleteDialog" max-width="500px">
        <v-card color="red-lighten-5">
          <v-card-title>Eliminar ActivoTag</v-card-title>
          <v-card-text>
            <!-- <v-text-field v-model.number="idToDelete" label="ID a eliminar" type="number"></v-text-field> -->
            <v-text-field v-model.number="actIdToDelete" label="Activo ID a eliminar" type="number"></v-text-field>
            <v-text-field v-model.number="tagIdToDelete" label="Tag ID a eliminar" type="number"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn color="red darken-1" text @click="eliminar(); showDeleteDialog = false">Eliminar</v-btn>
            <v-btn color="red-darken-4" text @click="showDeleteDialog = false">Cancelar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
  
  
      <v-dialog v-model="showNewDialog" max-width="500px">
        <v-card color="green-lighten-5">
          <v-card-title>Nuevo Activo Tag</v-card-title>
          <v-card-text>
            <v-text-field v-model.number="newActId" label="Activo ID" type="number"></v-text-field>
            <v-text-field v-model.number="newTagId" label="Tag ID" type="number"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn color="light-green-darken-3" @click="crearNuevoItem">Añadir</v-btn>
            <v-btn color="red-darken-4" text @click="showNewDialog = false">Cancelar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
  
  
      <!-- <v-dialog v-model="showUpdateDialog" max-width="500px">
        <v-card color="amber-lighten-5">
          <v-card-title>Actualizar ActivoTag</v-card-title>
          <v-card-text>
            <v-text-field v-model.number="updateId" label="ID a actualizar" type="number"></v-text-field>
            <v-text-field v-model.number="updateActId" label="Activo ID" type="number"></v-text-field>
            <v-text-field v-model.number="updateTagId" label="Tag ID" type="number"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn color="amber-accent-4" @click="actualizarElemento">Actualizar</v-btn>
            <v-btn color="red-darken-4" text @click="showUpdateDialog = false">Cancelar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog> -->
    </v-card>

</template>
  
  <script setup>
  import { ref } from 'vue';
  
  const search = ref('');
  const headers = ref([
    { align: 'start', key: 'id', sortable: false, title: 'ID' },
    { key: 'activoId', title: 'Activo ID' },
    { key: 'tagId', title: 'Tag ID' }
  ]);
  
  const datos = ref([]);
  
  //Funciones para crear un nuevo registro
  const showNewDialog = ref(false);
  const newActId = ref('');
  const newTagId = ref('');
  
  //Funciones para actualizar un registro
  // const showUpdateDialog = ref(false);
  // const updateId = ref('');
  // const updateActId = ref('');
  // const updateTagId = ref('');

  //Funciones para borrar un registro
  const showDeleteDialog = ref(false);
  const actIdToDelete = ref('');
  const tagIdToDelete = ref('');
  
  async function obtenerDatos() {
    try {
      const response = await fetch("https://localhost:4000/activotags");
      const data = await response.json();
      datos.value = data;
    } catch (error) {
      console.error('Error al obtener datos:', error);
    }
  }

  async function crearNuevoItem() {
    try {
      const response = await fetch('https://localhost:4000/activotags/asignar', {
        method: 'POST',
        body: JSON.stringify({    
          activoId: newActId.value,
          tagId: newTagId.value,
        }),
        headers: {
          'Content-type': 'application/json; charset=UTF-8',
        },
      });
      
      // if (!response.ok) {
      //   const data = await response.json();
      //   console.log(data);
      //   datos.value.push(data);
      showNewDialog.value = false;
      obtenerDatos();
      // } else {
        // throw new Error('Hubo un problema al crear el nuevo post.');
      // }
  
    } catch (error) {
      console.error('Error al crear el nuevo post:', error);
    }
  }
  
  // async function actualizarElemento() {
  //   try {
  //     const response = await fetch(`https://localhost:4000/activotags/${updateId.value}`, {
  //       method: 'PUT',
  //       body: JSON.stringify({
  //         id: updateId.value,
  //         activoId: updateActId.value,
  //         tagId: updateTagId.value,
  //       }),
  //       headers: {
  //         'Content-type': 'application/json; charset=UTF-8',
  //       },
  //     });
  
  //     // if (response.ok) {
  //     //   const updatedData = await response.json();
  //     //   console.log(updatedData);
  
  //     //   const index = datos.value.findIndex(post => post.id === updateId.value);
  //     //   if (index !== -1) {
  //     //     datos.value[index] = updatedData;
  //     //   }
  
  //     showUpdateDialog.value = false;
  //     obtenerDatos();
  //     // } else {
  //     //   throw new Error('Hubo un problema al actualizar el post.');
  //     // }
  
  //   } catch (error) {
  //     console.error('Error al actualizar el post:', error);
  //   }
  // }
  
  async function eliminar() {
    try {
      const response = await fetch('https://localhost:4000/activotags/desasignar', {
        method: 'DELETE',
        body: JSON.stringify({
          activoId: actIdToDelete.value,
          tagId: tagIdToDelete.value
        }),
        headers: {
            'Content-type': 'application/json; charset=UTF-8',
        },
      });
      obtenerDatos();
      // const data = await response.json();
      // console.log(data);
      // const index = datos.value.findIndex(post => post.id === id);
      // if (index !== -1) {
      //   datos.value.splice(index, 1);
      // }
  
    } catch (error) {
      console.error('Error al crear el delete:', error);
    }
  }
  
  obtenerDatos();
</script>
  