<script setup lang="ts">
import { computed, ref } from "vue";
import draggable from "vuedraggable";

type ColumnKind = "good" | "bad" | "improve" | "shout-out" | "action";

interface Note {
  id: number;
  text: string;
  column: ColumnKind;
}

interface DragEvent {
  oldIndex: number;
  newIndex: number;
  item: HTMLElement;
  from: HTMLElement;
  to: HTMLElement;
}

const notes = ref<Note[]>([]);
const editingColumn = ref<ColumnKind | null>(null);
const newNoteText = ref("");

const goodNotes = computed(() =>
  notes.value.filter((n) => n.column === "good"),
);
const badNotes = computed(() =>
  notes.value.filter((n) => n.column === "bad"),
);
const improveNotes = computed(() =>
  notes.value.filter((n) => n.column === "improve"),
);
const shoutOutNotes = computed(() =>
  notes.value.filter((n) => n.column === "shout-out"),
);
const actionNotes = computed(() =>
  notes.value.filter((n) => n.column === "action"),
);

const startEditing = (
  column: ColumnKind,
) => {
  editingColumn.value = column;
  newNoteText.value = "";
};

const cancelEditing = () => {
  editingColumn.value = null;
  newNoteText.value = "";
};

const addNote = (payload: KeyboardEvent) => {
  if (payload.shiftKey) {
    return;
  }
  if (newNoteText.value.trim() && editingColumn.value) {
    notes.value.push({
      id: Date.now(),
      text: newNoteText.value.trim(),
      column: editingColumn.value,
    });
    newNoteText.value = "";
    editingColumn.value = null;
  }
};

const removeNote = (id: number) => {
  notes.value = notes.value.filter((note) => note.id !== id);
};

const moveNote = (evt: DragEvent, newColumn: ColumnKind) => {
  const noteId = evt.item.getAttribute("data-id");
  const note = notes.value.find(
    (n) => n.id === Number.parseInt(noteId || "0", 10),
  );
  if (note) {
    note.column = newColumn;
  }
};

const handleDragEnd = (evt: DragEvent) => {
  const fromColumn = evt.from.getAttribute("data-column") as ColumnKind;
  const toColumn = evt.to.getAttribute("data-column") as ColumnKind;

  if (fromColumn && toColumn && fromColumn !== toColumn) {
    moveNote(evt, toColumn);
  }
};
</script>

<template>
  <div class="retro-board">
    <h1>Retrospective Board</h1>

    <div class="columns">
      <div class="column">
        <h2>What went well?</h2>
        <div class="add-card">
          <button v-if="editingColumn !== 'good'" @click="startEditing('good')" class="add-card-btn">
            + Add Card
          </button>
          <div v-else class="new-card-input">
            <textarea v-model="newNoteText" placeholder="Enter your note..." @keyup.enter="addNote"
              @keyup.esc="cancelEditing" ref="input"></textarea>
            <div class="new-card-actions">
              <button @click="addNote" class="save-btn">Save</button>
              <button @click="cancelEditing" class="cancel-btn">Cancel</button>
            </div>
          </div>
        </div>

        <draggable v-model="goodNotes" :group="{ name: 'notes', pull: true, put: true }" item-key="id" class="notes"
          :data-column="'good'" @end="handleDragEnd" force-fallback fallback-class="sortable-drag">
          <template #item="{ element }">
            <div class="note" :data-id="element.id">
              {{ element.text }}
              <button @click="removeNote(element.id)" class="delete-btn">×</button>
            </div>
          </template>
        </draggable>
      </div>

      <div class="column">
        <h2>What didn't go well?</h2>
        <div class="add-card">
          <button v-if="editingColumn !== 'bad'" @click="startEditing('bad')" class="add-card-btn">
            + Add Card
          </button>
          <div v-else class="new-card-input">
            <textarea v-model="newNoteText" placeholder="Enter your note..." @keyup.enter="addNote"
              @keyup.esc="cancelEditing" ref="input"></textarea>
            <div class="new-card-actions">
              <button @click="addNote" class="save-btn">Save</button>
              <button @click="cancelEditing" class="cancel-btn">Cancel</button>
            </div>
          </div>
        </div>
        <draggable v-model="badNotes" :group="{ name: 'notes', pull: true, put: true }" item-key="id" class="notes"
          :data-column="'bad'" @end="handleDragEnd" force-fallback fallback-class="sortable-drag">
          <template #item="{ element }">
            <div class="note" :data-id="element.id">
              {{ element.text }}
              <button @click="removeNote(element.id)" class="delete-btn">×</button>
            </div>
          </template>
        </draggable>
      </div>

      <div class="column">
        <h2>What could be improved?</h2>
        <div class="add-card">
          <button v-if="editingColumn !== 'improve'" @click="startEditing('improve')" class="add-card-btn">
            + Add Card
          </button>
          <div v-else class="new-card-input">
            <textarea v-model="newNoteText" placeholder="Enter your note..." @keyup.enter="addNote"
              @keyup.esc="cancelEditing" ref="input"></textarea>
            <div class="new-card-actions">
              <button @click="addNote" class="save-btn">Save</button>
              <button @click="cancelEditing" class="cancel-btn">Cancel</button>
            </div>
          </div>
        </div>
        <draggable v-model="improveNotes" :group="{ name: 'notes', pull: true, put: true }" item-key="id" class="notes"
          :data-column="'improve'" @end="handleDragEnd" force-fallback fallback-class="sortable-drag">
          <template #item="{ element }">
            <div class="note" :data-id="element.id">
              {{ element.text }}
              <button @click="removeNote(element.id)" class="delete-btn">×</button>
            </div>
          </template>
        </draggable>
      </div>

      <div class="column">
        <h2>Shout outs</h2>
        <div class="add-card">
          <button v-if="editingColumn !== 'shout-out'" @click="startEditing('shout-out')" class="add-card-btn">
            + Add Card
          </button>
          <div v-else class="new-card-input">
            <textarea v-model="newNoteText" placeholder="Enter your note..." @keyup.enter="addNote"
              @keyup.esc="cancelEditing" ref="input"></textarea>
            <div class="new-card-actions">
              <button @click="addNote" class="save-btn">Save</button>
              <button @click="cancelEditing" class="cancel-btn">Cancel</button>
            </div>
          </div>
        </div>
        <draggable v-model="shoutOutNotes" :group="{ name: 'notes', pull: true, put: true }" item-key="id" class="notes"
          :data-column="'shout-out'" @end="handleDragEnd" force-fallback fallback-class="sortable-drag">
          <template #item="{ element }">
            <div class="note" :data-id="element.id">
              {{ element.text }}
              <button @click="removeNote(element.id)" class="delete-btn">×</button>
            </div>
          </template>
        </draggable>
      </div>

      <div class="column">
        <h2>Action items</h2>
        <div class="add-card">
          <button v-if="editingColumn !== 'action'" @click="startEditing('action')" class="add-card-btn">
            + Add Card
          </button>
          <div v-else class="new-card-input">
            <div><textarea v-model="newNoteText" placeholder="Enter your note..." @keyup.enter="addNote"
                @keyup.esc="cancelEditing" ref="input"></textarea></div>
            <div class="new-card-actions">
              <button @click="addNote" class="save-btn">Save</button>
              <button @click="cancelEditing" class="cancel-btn">Cancel</button>
            </div>
          </div>
        </div>
        <draggable v-model="actionNotes" :group="{ name: 'notes', pull: true, put: true }" item-key="id" class="notes"
          :data-column="'action'" @end="handleDragEnd" force-fallback fallback-class="sortable-drag">
          <template #item="{ element }">
            <div class="note" :data-id="element.id">
              {{ element.text }}
              <button @click="removeNote(element.id)" class="delete-btn">×</button>
            </div>
          </template>
        </draggable>
      </div>
    </div>
  </div>
</template>

<style scoped>
.retro-board {
  margin: 0 auto;
  padding: 20px;
}

h1 {
  text-align: center;
  margin-bottom: 3rem;
}

.columns {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 20px;
  width: 100%;
}

.column {
  background-color: #f5f5f5;
  border-radius: 8px;
  padding: 15px;
  min-height: 400px;
  min-width: 240px;

  display: flex;
  flex-direction: column;
}

.column h2 {
  color: #2c3e50;
  margin-bottom: 15px;
  font-size: 18px;
}

.notes {
  display: flex;
  flex-direction: column;
  gap: 10px;
  height: 100%;
  padding: 10px;
  border-radius: 4px;
  background-color: rgba(0, 0, 0, 0.02);
}

.note {
  background-color: white;
  padding: 12px;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  position: relative;
  display: flex;
  justify-content: space-between;
  align-items: center;
  cursor: grab;
  user-select: none;
  transition: transform 0.2s ease;
  color: #444;
  white-space: pre-wrap;
  text-align: left;
}

.note:active {
  cursor: grabbing;
  transform: scale(1.02);
}

.sortable-ghost {
  opacity: 0.5;
  background: #c8ebfb;
}

.sortable-drag {
  opacity: 0.9;
  background: #ffffff;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  transform: rotate(2deg);
}

.delete-btn {
  background-color: #ff4444;
  color: white;
  border: none;
  border-radius: 50%;
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  font-size: 16px;
  padding: 0;
}

.delete-btn:hover {
  background-color: #cc0000;
}

.add-card {
  margin-bottom: 10px;

  &:has(.new-card-input) {
    padding: 0 10px;
  }
}

.add-card-btn {
  width: 100%;
  padding: 8px;
  background-color: transparent;
  border: 2px dashed #42b883;
  color: #42b883;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.add-card-btn:hover {
  background-color: rgba(66, 184, 131, 0.1);
}

.new-card-input {
  display: flex;
  flex-direction: column;
  gap: 8px;
  background-color: white;
  padding: 12px;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.new-card-input textarea {
  flex: 1;
  width: 100%;
  margin-bottom: 8px;
  border: 1px solid #ddd;
  padding: 8px;
  border-radius: 4px;
  font-size: 14px;
  background-color: #ffffff;
  color: #444;
  height: auto;
  resize: vertical;
}

.new-card-actions {
  display: flex;
  gap: 8px;
  justify-content: flex-end;
}

.save-btn {
  background-color: #42b883;
  color: white;
  border: none;
  padding: 6px 12px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
}

.cancel-btn {
  background-color: #ff4444;
  color: white;
  border: none;
  padding: 6px 12px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
}

.save-btn:hover {
  background-color: #3aa876;
}

.cancel-btn:hover {
  background-color: #cc0000;
}
</style>
