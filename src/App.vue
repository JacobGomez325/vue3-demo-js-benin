<template>
  <div class="container mx-auto px-8 py-6 ">
    <vmodal v-if="showModal">
      <div class="w-[60vw] p-3">
        <form 
         
          @submit.prevent="sendData" 
          class=" flex flex-col space-y-3">
          <input type="text" v-model="formData.titre" placeholder="titre de la note " class="border border-gray-900  px-3 py-2 text-sm w-full">
          <textarea name="note" v-model.trim="formData.contenu" id="note" cols="30" rows="10" placeholder="contenu de la note" class="border border-gray-900  px-3 py-2 text-sm w-full"></textarea>
          
          <button type="submit" 
            v-if="!isUpdate" 
            key="ajouter" 
            class="bg-blue-500 px-4 py-2  text-white uppercase font-semibold"
            >ajouter une note</button>
          <button 
            type="submit" 
            v-else 
            key="modifier" 
            class="bg-blue-500 px-4 py-2  text-white uppercase font-semibold"
            >modifier une note</button>
          <button  
            @click="closeModal" 
            class="bg-red-500 text-white px-4 py-2 font-semibold uppercase" 
            >fermer</button>
        </form>
        
      </div>
    </vmodal>
    <nav class="flex justify-between py-4">
      <h1 class="text-3xl font-bold ">Notes</h1>
      <button @click="addNote" class="text-3xl rounded-full px-2 py-1 text-center bg-black text-white">+</button>
    </nav>
    
   
   <div class="grid grid-cols-4 gap-4">
    <Note v-for="(note,index) in mesNotes" 
    :key="index" 
    :id= "note.id"
    :index= "index"
    :titre="note.titre" 
    :contenu="note.contenu" 
    :couleur="note.couleur" 
    :date-creation="note.dateCreation"
    @modifier="updateData"
    @supprimer="deleteData"
     /> 
   </div>
   

  </div>
</template>

<script setup lang="ts">
  import {ref,reactive} from 'vue'
  import Note from '@/components/Note.vue'
  import Vmodal from './components/Vmodal.vue';

  interface NoteT {
    id:number;
    titre: string,
    contenu: string,
    couleur:string,
    dateCreation:Date
  }
  const elementIndex = ref<number>(0)
  const isUpdate = ref<boolean>(false)
  const showModal  = ref<boolean>(false)
  const formData = reactive<NoteT>({
    id:0,
    titre: '',
    contenu: '',
    couleur: '',
    dateCreation: new Date()
  })
  

  let mesNotes = ref<NoteT[]>([])


  const  getRandomColor = () =>  {
    return "hsl(" + Math.random() * 360 + ", 100%, 75%)";
  }

  function addNote() {
    showModal.value = true
    isUpdate.value = false
  }

  function closeModal() {
    formData.titre = '' 
    formData.contenu = ''
    showModal.value = false

  }
  function sendData() {
    if(!isUpdate.value) {
      formData.id = Math.floor(Math.random() * 1000000)
      formData.couleur = getRandomColor()
      const postData = {...formData}
      //const postData = Object.assign({},formData) // seconde  maniere de copier l'object 
      mesNotes.value.push(postData)
      closeModal()
    }else {
      const updateData = {...formData}
      mesNotes.value[elementIndex.value] = updateData
      closeModal()
    }

  }


  function updateData(id:number,index:number) {
    isUpdate.value = true
    showModal.value = true
    elementIndex.value = index
    mesNotes.value.filter((note) => {
      if(note.id === id) {
        formData.id = note.id
        formData.titre = note.titre
        formData.contenu = note.contenu
        formData.dateCreation = note.dateCreation
        formData.couleur = note.couleur
      }
    })
    
  }
  function deleteData(id:number) {
   mesNotes.value = mesNotes.value.filter((note) => note.id !== id)
  
  }
</script>


<style  scoped>

</style>