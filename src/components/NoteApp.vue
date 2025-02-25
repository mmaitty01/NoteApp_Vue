<template>
  <div class="hidden bg-blue-500 bg-green-700 bg-yellow-200 "></div>

  <div class="p-4 max-w-4xl mx-auto">
    <h1 class="text-xl font-bold mb-4 text-center">📝 Note App</h1>
    
    <!-- ตาราง 4 คอลัมน์ -->
    <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
      <!-- ปุ่มเพิ่มโน้ต -->
      <div class="bg-blue-500 w-50 h-50 npmtext-white flex items-center justify-center p-4 rounded-md cursor-pointer"
           @click="showPopup = true">
        <span class="text-lg text-white font-bold">+ Add Note</span>
      </div>
      
     <!-- แสดงโน้ต -->
      <div v-for="(note, index) in notes" :key="index"
           :class="['p-4 rounded-md relative w-50 h-50 flex items-center overflow-hidden', note.color]">
        <p class="break-words line-clamp-3">{{ note.text.length > 100 ? note.text.substring(0, 100) + '...' : note.text }}</p>
        <div class="absolute top-2 right-2 flex gap-2">
          <button @click="editNote(index)" class="w-15  bg-sky-500 text-blue-400 px-2 py-1 rounded-md">
            <img src="../assets/pen.png"/>
          </button>
          <button @click="deleteNote(index)" class="w-15 bg-red-500 text-red-500 px-2 py-1 rounded-md">
            <img src="../assets/bin.png"/>
          </button>
        </div>
        <div class="absolute bottom-2 right-2 flex gap-2">
          <button @click="openNoteViewer(index)" class="bg-white text-blue-400  px-2 py-1 rounded-md">Read more</button>
        </div>
        
      </div>
    </div>
    
    <!-- ป็อปอัพเพิ่ม/แก้ไขโน้ต -->
    <div v-if="showPopup" class="fixed inset-0 bg-black/50 bg-opacity-50 flex items-center justify-center">
      <div class="p-6 rounded-lg w-2/3 md:w-1/3" :class="selectedColor">
        <h2 class="text-lg font-bold mb-2">{{ editingIndex !== null ? 'Edit Note' : 'New Note' }}</h2>
        <textarea v-model="newNote" class="w-full p-2 border rounded-md" rows="9"></textarea>
        
        <!-- ปุ่มเลือกสีพื้นหลัง -->
        <div class="flex justify-center gap-2 mt-2">
          <button v-for="color in colors" :key="color" @click="selectedColor = color"
                  :class="['w-8 h-8 rounded-full', color, selectedColor === color ? 'ring-2 ring-black' : '']">
          </button>
        </div>
        
        <div class="flex justify-end gap-2 mt-4">
          <button @click="showPopup = false; editingIndex = null ; newNote='';selectedColor='bg-gray-100'"  class="bg-red-500 text-white px-4 py-2 rounded-md">Cancel</button>
          <button @click="addOrUpdateNote" class="bg-green-500 text-white px-4 py-2 rounded-md">Save</button>
        </div>
      </div>
    </div>

     <!-- Note Viewer Popup -->
     <div v-if="showViewer" class="fixed inset-0 bg-black/50 bg-opacity-50 flex items-center justify-center">
      <div class="p-6 rounded-lg w-2/3 md:w-1/3" :class="notes[selectedNoteIndex].color">
        <h2 class="text-lg font-bold mb-2">Note</h2>
        <p class="whitespace-pre-wrap break-words">{{ notes[selectedNoteIndex].text }}</p>
        <div class="flex justify-end mt-4">
          <button @click="showViewer = false" class="bg-red-500 text-white px-4 py-2 rounded-md">Close</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';

export default {
  setup() {
    const notes = ref([]);
    const newNote = ref('');
    const editingIndex = ref(null);
    const showPopup = ref(false);
    const showViewer = ref(false);
    const selectedNoteIndex = ref(null);
    const selectedColor = ref('bg-gray-100');
    const colors = ref(['bg-red-200', 'bg-yellow-200', 'bg-green-200','bg-cyan-200','bg-purple-200']);

    onMounted(() => {
      const savedNotes = localStorage.getItem('notes');
      if (savedNotes) {
        notes.value = JSON.parse(savedNotes);
      }
    });

    const saveNotes = () => {
      localStorage.setItem('notes', JSON.stringify(notes.value));
    };

    const addOrUpdateNote = () => {
      if (newNote.value.trim() !== '') {
        if (editingIndex.value !== null) {
          notes.value[editingIndex.value].text = newNote.value;
          notes.value[editingIndex.value].color = selectedColor.value;
          editingIndex.value = null;
        } else {
          notes.value.push({ text: newNote.value, color: selectedColor.value });
        }
        newNote.value = '';
        selectedColor.value = 'bg-gray-100';
        showPopup.value = false;
        saveNotes();
      }
    };
    

    const openNoteViewer = (index) => {
      selectedNoteIndex.value = index;
      showViewer.value = true;
    };

    const editNote = (index) => {
      newNote.value = notes.value[index].text;
      selectedColor.value = notes.value[index].color;
      editingIndex.value = index;
      showPopup.value = true;
    };

    const deleteNote = (index) => {
      notes.value.splice(index, 1);
      saveNotes();
    };

    return { notes, newNote, editingIndex, showPopup, selectedColor, colors, addOrUpdateNote, editNote, deleteNote , showViewer, selectedNoteIndex,openNoteViewer};
  },
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
}
</style>
