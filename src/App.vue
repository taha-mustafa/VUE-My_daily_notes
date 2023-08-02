<template>
  <main class="main-project">
    <div v-show="toggleShow" class="overlay-show">
      <div class="add-note-manually">
        <textarea v-model.trim="addNewNote" name="note" id="add-note" cols="25" rows="10"></textarea>
        <p class="error-msg" v-if="showErrorMsg">{{ showErrorMsg }}</p>
        <div class="btns">
          <button @click="addYourNote">Add A Note</button>
          <button @click="toggleShow = false">Close</button>
        </div>
      </div>
    </div>
    <div class="notes-container">
      <header>
        <h1>MY Daily Notes</h1>
        <button @click="toggleShow = true"><i class="fa-solid fa-plus"></i></button>
      </header>
      <div class="notes-place">
        <div v-for="note in notes" :key="note.noteId" :style="{backgroundColor: note.noteBackGround}" class="a_note">
          <p :style="{ overflow: notes.length < 220 ? 'hidden' : 'auto', 'overflow-y': notes.length > 220 ? 'scroll' : 'auto' }" class="note-text">{{ note.noteText }}</p>
          <div class="separated-spans">
            <span class="note-date">{{ note.noteDate }}</span>
            <button @click="deleteNoteById(note.noteId)" class="delete-note"><i class="fa-solid fa-x"></i></button>
            <span class="note-id" id="id-num">{{ note.noteId }}</span>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const toggleShow = ref(false)
const addNewNote = ref('')
const notes = ref([])
const showErrorMsg = ref('')


/* An Helped Function From StackOverFlow Community */
function getDarkColor() {
    let color = '#';
    for (let i = 0; i < 6; i++) {
        color += Math.floor(Math.random() * 10);
    }
    return color;
}

/* Function To Add a new NOTE if you WISH */
const addYourNote = () => {
  /* Check Number of letters inside the note body */
  if (addNewNote.value.length < 15) {
    setTimeout(() => {
      showErrorMsg.value = '';
    }, 4000);
    return showErrorMsg.value = 'Your Note Must be 15 characters or more!';
  }

/* Store Notes inside the notes Array */
  notes.value.push({
    noteText: addNewNote.value,
    noteId: Math.floor(Math.random() * 200000),
    noteDate: new Date().toLocaleString('en-GB').split(',')[0],
    noteBackGround: getDarkColor(),
  });
  toggleShow.value = false;
  addNewNote.value = '';
  saveNotes();
}

/* Function To Delete a note by id */
const deleteNoteById = (noteId) => {
  const noteIndex = notes.value.findIndex(note => note.noteId === noteId)
  if (noteIndex !== -1) {
    notes.value.splice(noteIndex, 1)
    saveNotes();
  }
}

/* Function To Save Notes to localStorage */
const saveNotes = () => {
  localStorage.setItem('notes', JSON.stringify(notes.value));
}

/* Function To Load Notes from localStorage */
const loadNotes = () => {
  const storedNotes = localStorage.getItem('notes');
  if (storedNotes) {
    notes.value = JSON.parse(storedNotes);
  }
}

onMounted(() => {
  loadNotes();
});
</script>

<style scoped>
.notes-container {
  margin: 0 auto;
  max-width: 1000px;
  padding: 10px;
}
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  box-shadow: 1px 9px 30px #afe7e7;
  border-radius: 10px;
  margin-top: 30px;
  margin-bottom: 30px;
}
@media (max-width: 550px) {
  header {
    flex-direction: column;
    padding: 13px;
  }
}
header h1 {
  color: azure;
  font-size: 2.2rem;
  letter-spacing: -1px;
  user-select: none;
  transition: .4s;
}
@media (max-width: 550px) {
  header h1 {
    font-size: 1.7rem;
    margin-top: 8px;
    margin-bottom: 8px;
  }
}
header button {
  padding: 5px;
  border: none;
  background-color: cadetblue;
  color: azure;
  font-size: 2rem;
  cursor: pointer;
  transition: .4s;
}
@media (max-width: 550px) {
  header button {
    font-size: 1.7rem;
    padding: 0;
  }
}
header h1:hover,
header button:hover {
  color: #afe7e7;
}
.notes-place {
  display: flex;
  justify-content: center;
  gap: 1rem;
  flex-wrap: wrap;
}
.notes-place .a_note {
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  width: 250px;
  height: 250px;
  border-radius: 10px;
  padding: 10px;
}
.a_note .note-text {
  line-height: 1.25;
  font-size: 20px;
  font-weight: bold;
  overflow-wrap: break-word;
  margin-bottom: 5px;
}
.a_note .separated-spans {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.a_note .separated-spans span {
  font-size: 18px;
}
.separated-spans .delete-note {
  width: 30px;
  height: 30px;
  border: none;
  padding: 5px;
  border-radius: 50%;
  cursor: pointer;
  background-color: #afe7e7;
  color: rgb(52, 54, 54);
  transition: .3s;
}
.separated-spans .delete-note:hover {
  background-color: rgb(52, 54, 54);
  color: #afe7e7;
}
.a_note .note-text,
.a_note .separated-spans span {
  color: azure;
}
.main-project .overlay-show {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgba(91, 89, 89, 0.728);
  z-index: 100;
  display: flex;
  justify-content: center;
  align-items: center;
}
.overlay-show .add-note-manually {
  width: 750px;
  background-color: azure;
  border-radius: 10px;
  padding: 30px;
  position: relative;
  display: flex;
  flex-direction: column;
  justify-content: center;
  top: -13%;
}
@media (min-width: 600px) and (max-width: 850px) {
  .overlay-show .add-note-manually {
    width: 550px;
  }
}
@media (max-width: 600px) {
  .overlay-show .add-note-manually {
    width: 300px;
    top: -20px;
  }
}
.add-note-manually textarea {
  margin-bottom: 8px;
  border: 2px dotted rgba(91, 89, 89, 0.728);
  outline: none;
  resize: none;
  font-size: 20px;
  padding: 8px;
}
.add-note-manually .error-msg {
  color: red;
  margin: 5px 0 5px 0;
  font-size: 18px;
  font-weight: 500;
}
.add-note-manually .btns {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
}
.add-note-manually .btns > button {
  outline: none;
  border: none;
  padding: 10px;
  border-radius: 10px;
  font-size: 17px;
  cursor: pointer;
  background-color: #afe7e7;
  color: rgb(52, 54, 54);
  font-weight: 600;
  transition: .4s;
}
.add-note-manually button:hover {
  background-color: rgb(52, 54, 54);
  color: #afe7e7;
}
</style>