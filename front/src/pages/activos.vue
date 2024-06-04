<template>
    <v-card max-width="1000px" hover title="Activos" color="pink-lighten-5">
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
          <v-col cols="auto">
            <v-btn 
              elevation="4" 
              density="comfortable" 
              color="amber-lighten-4" @click="showUpdateDialog = true">
              Actualizar
            </v-btn>
          </v-col>
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
        :sort-by="[{ key: 'id', order: 'asc' }]" :sort-by1="[{ key: 'numSerie', order: 'asc' }]"
        :sort-by2="[{ key: 'numInventario', order: 'asc' }]"></v-data-table>
  
  
      <v-dialog v-model="showDeleteDialog" max-width="500px">
        <v-card color="red-lighten-5">
          <v-card-title>Eliminar Activo</v-card-title>
          <v-card-text>
            <v-text-field 
              v-model.number="idToDelete" 
              label="ID a eliminar" 
              type="number"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn 
              color="red darken-1" 
              text @click="eliminar(idToDelete); 
              showDeleteDialog = false">
              Eliminar
            </v-btn>
            <v-btn 
              color="red-darken-4" 
              text @click="showDeleteDialog = false">
              Cancelar
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
  
  
      <v-dialog v-model="showNewDialog" max-width="500px">
        <v-card color="green-lighten-5">
          <v-card-title>Nuevo Activo</v-card-title>
          <v-card-text>
            <v-text-field v-model.number="newNumSerie" label="Número de serie" type="number"></v-text-field>
            <v-text-field v-model.number="newNumInventario" label="Número de inventario" type="number"></v-text-field>
            <v-text-field v-model="newDesc" label="Descripción"></v-text-field>
            <v-text-field v-model.number="newUbi" label="Ubicación" type="number"></v-text-field>
            <v-text-field v-model.number="newResponsable" label="Responsable" type="number"></v-text-field>
            <v-file-input v-model="newImagen" label="Imagen"></v-file-input>
          </v-card-text>
          <v-card-actions>
            <v-btn color="light-green-darken-3" @click="crearNuevoItem">Añadir</v-btn>
            <v-btn color="red-darken-4" text @click="showNewDialog = false">Cancelar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
  
  
      <v-dialog v-model="showUpdateDialog" max-width="500px">
        <v-card color="amber-lighten-5">
          <v-card-title>Actualizar Activo</v-card-title>
          <v-card-text>
            <v-text-field v-model.number="updateId" label="ID a actualizar" type="number"></v-text-field>
            <v-text-field v-model.number="updateNumSerie" label="Número de serie" type="number"></v-text-field>
            <v-text-field v-model="updateNumInventario" label="Número de inventario" type="number"></v-text-field>
            <v-text-field v-model="updateDesc" label="Descripción"></v-text-field>
            <v-text-field v-model.number="updateUbi" label="Ubicación" type="number"></v-text-field>
            <v-text-field v-model.number="updateResponsable" label="Responsable" type="number"></v-text-field>
            <v-file-input v-model="updateImagen" label="Imagen"></v-file-input>
          </v-card-text>
          <v-card-actions>
            <v-btn color="amber-accent-4" @click="actualizarElemento">Actualizar</v-btn>
            <v-btn color="red-darken-4" text @click="showUpdateDialog = false">Cancelar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
  
    </v-card>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  
  const search = ref('');
  const headers = ref([
    { align: 'start', key: 'id', sortable: false, title: 'ID' },
    { key: 'numSerie', title: 'No. Serie' },
    { key: 'numInventario', title: 'No. Inventario' },
    { key: 'descripcion', title: 'Descripción' },
    { key: 'responsable', title: 'Responsable' },
    { key: 'ubicacion', title: 'Ubicación' },
    { key: 'imagen', title: 'Imagen' }
  ]);
  
  const datos = ref([]);
  
  //Funciones para crear un nuevo registro
  const showNewDialog = ref(false);
  const newNumSerie = ref('');
  const newNumInventario = ref('');
  const newDesc = ref('');
  const newResponsable = ref('');
  const newUbi = ref('');
  const newImagen = ref(null);
  
  //Funciones para actualizar un registro
  const showUpdateDialog = ref(false);
  const updateId = ref('');
  const updateNumSerie = ref('');
  const updateNumInventario = ref('');
  const updateDesc = ref('');
  const updateResponsable = ref('');
  const updateUbi = ref('');
  const updateImagen = ref(null);
  
  //Funciones para borrar un registro
  const showDeleteDialog = ref(false);
  const idToDelete = ref('');
  
  async function obtenerDatos() {
    try {
      const response = await fetch("https://localhost:4000/activos");
      const data = await response.json();
      datos.value = data;
    } catch (error) {
      console.error('Error al obtener datos:', error);
    }
  }

  async function crearNuevoItem() {
    try {
      const response = await fetch('https://localhost:4000/activos', {
        method: 'POST',
        body: JSON.stringify({
          numSerie: newNumSerie.value,
          numInventario: newNumInventario.value,
          descripcion: newDesc.value,
          ubicacion: newUbi.value,
          responsable: newResponsable.value,
          imagen: newImagen.value,
        }),
        headers: {
          'Content-type': 'application/json; charset=UTF-8',
        },
      });
  
      
        // const data = await response.json();
        // console.log(data);
        // datos.value.push(data);
        showNewDialog.value = false;
        obtenerDatos();
    } catch (error) {
      console.error('Error al crear el nuevo post:', error);
    }
  }
  
  async function actualizarElemento() {
    try {
      const response = await fetch(`https://localhost:4000/activos/${updateId.value}`, {
        method: 'PUT',
        body: JSON.stringify({
          id: updateId.value,
          numSerie: updateNumSerie.value,
          numInventario: updateNumInventario.value,
          descripcion: updateDesc.value,
          ubicacion: updateUbi.value,
          responsable: updateResponsable.value,
          imagen: updateImagen.value,
        }),
        headers: {
          'Content-type': 'application/json; charset=UTF-8',
        },
      });
  
      // if (response.ok) {
        // const updatedData = await response.json();
        // console.log(updatedData);
  
        // const index = datos.value.findIndex(post => post.id === updateId.value);
        // if (index !== -1) {
        //   datos.value[index] = updatedData;
        // }
  
      showUpdateDialog.value = false;
      obtenerDatos();
      // } else {
      //   throw new Error('Hubo un problema al actualizar el post.');
      // }
  
    } catch (error) {
      console.error('Error al actualizar el post:', error);
    }
  }
  
  async function eliminar(id) {
    try {
      const response = await fetch(`https://localhost:4000/activos/${id}`, {
        method: 'DELETE',
      });
      obtenerDatos();
    } catch (error) {
      console.error('Error al crear el delete:', error);
    }
  }
  
  obtenerDatos();
</script>