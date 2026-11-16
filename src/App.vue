<script setup>
import { ref, computed } from "vue";

const folders = ref([
  { id: 1, name: "Funny", icon: "😂" },
  { id: 2, name: "Cute", icon: "💕" },
  { id: 3, name: "Sarcastic", icon: "💀" },
]);

const folderDialog = ref(null);
const newFolderName = ref("");

const addFolder = () => {
  const name = newFolderName.value.trim();

  if (!name) return;

  folders.value.push({
    id: Date.now(),
    name,
    icon: "📁",
  });

  newFolderName.value = "";
  folderDialog.value.close();
};

const deleteDialog = ref(null);
const folderToDelete = ref(null);

const confirmDelete = () => {
  folders.value = folders.value.filter((f) => f.id !== folderToDelete.value);

  deleteDialog.value.close();
};

const requestDelete = (id) => {
  folderToDelete.value = id;
  deleteDialog.value.showModal();
};

/// pictures appearing
const selectedFolderId = ref(null);
const stickers = ref([]);
const filterdStickers = computed(() => {
  if (selectedFolderId.value === null) {
    return stickers.value;
  }
  return stickers.value.filter(
    (sticker) => sticker.folderId === selectedFolderId.value,
  );
});

const addPictureDialog = ref(null);
const newStickerUrl = ref("");
const newStickerName = ref("");

const addSticker = () => {
  if (!newStickerUrl.value.trim()) return;

  stickers.value.push({
    id: Date.now(),
    url: newStickerUrl.value.trim(),
    name: newStickerName.value.trim() || "New Reaction",
    folderId: selectedFolderId.value,
  });

  newStickerUrl.value = "";
  newStickerName.value = "";
  addPictureDialog.value.close();
};
</script>

<template>
  <div class="app">
    <!-- Sidebar -->
    <aside class="sidebar">
      <div>
        <div class="sidebar__drag"></div>

        <div class="sidebar__label">MY COLLECTION</div>

        <nav>
          <button
            class="sidebar__item"
            :class="{ 'is-active': selectedFolderId === null }"
            @click="selectedFolderId = null"
          >
            ♡ All Reactions
          </button>

          <a href="#" class="sidebar__item"> ☆ Favourites </a>
        </nav>

        <div class="sidebar__label sidebar__label--spaced">MY FOLDERS</div>

        <nav>
          <div
            v-for="folder in folders"
            :key="folder.id"
            class="sidebar__folder"
          >
            <button
              class="sidebar__item"
              :class="{ 'is-active': selectedFolderId === folder.id }"
              @click="selectedFolderId = folder.id"
            >
              <span>{{ folder.icon }} {{ folder.name }}</span>
            </button>

            <button
              @click="requestDelete(folder.id)"
              aria-label="Delete Folder"
            >
              🗑
            </button>
          </div>
        </nav>

        <button class="sidebar__new" @click="folderDialog.showModal()">
          + New Folder
        </button>
        <dialog ref="folderDialog">
          <form @submit.prevent="addFolder">
            <h3>New Folder</h3>

            <input
              v-model="newFolderName"
              placeholder="Folder name..."
              autofocus
            />

            <button type="submit">Create</button>

            <button type="button" @click="folderDialog.close()">Cancel</button>
          </form>
        </dialog>
        <dialog ref="deleteDialog">
          <div class="delete-popup">
            <h3>Delete Folder?</h3>

            <p>Are you sure you want to delete this folder?</p>

            <button @click="confirmDelete">Delete</button>

            <button @click="deleteDialog.close()">Cancel</button>
          </div>
        </dialog>
        <dialog ref="addPictureDialog">
          <form @submit.prevent="addSticker">
            <h3>Add Sticker</h3>

            <input
              v-model="newStickerUrl"
              placeholder="Sticker URL..."
              autofocus
            />
            <input v-model="newStickerName" placeholder="Sticker name..." />
            <button type="submit">Add</button>
            <button type="button" @click="addPictureDialog.close()">
              Cancel
            </button>
          </form>
        </dialog>
      </div>
    </aside>

    <!-- Main content -->
    <main class="main">
      <!-- Toolbar -->
      <header class="toolbar">
        <div class="toolbar__titles">
          <h1 class="toolbar__title">All Reactions</h1>

          <span class="toolbar__count"> 0 stickers </span>
        </div>

        <input type="text" class="toolbar__search" placeholder="Search..." />

        <button class="toolbar__btn" @click="addPictureDialog.showModal()">
          + Add
        </button>
      </header>

      <!-- Sticker collection -->
      <section class="paper">
        <div class="grid">
          <div
            v-for="sticker in filterdStickers"
            :key="sticker.id"
            class="sticker"
          >
            <div class="sticker__tape"></div>
            <div class="sticker__img">
              <img :src="sticker.url" :alt="sticker.name" />
            </div>
            <div class="sticker__name">{{ sticker.name }}</div>
          </div>
        </div>
      </section>
    </main>
  </div>
</template>
