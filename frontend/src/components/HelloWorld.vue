<template>
  <div class="d-flex justify-content-center align-items-center bg-light">
    <div class="container bg-white shadow rounded p-4" style="max-width: 1200px; width: 100%; min-height: 80vh;">
      <div class="d-flex">
        <!-- Sidebar -->
        <aside class="bg-white p-4 border-end" >
          <button class="btn btn-primary w-100 mb-3">📥 Import Documents</button>
          
          <div class="mb-3">
            <p class="small fw-semibold mb-1">Storage Usage</p>
            <div class="progress">
              <div class="progress-bar bg-primary" style="width: 6%;"></div>
            </div>
            <p class="small text-muted mt-1">6% used of 2GB</p>
          </div>
          
          <input type="text" placeholder="🔍 Search" class="form-control mb-3" />
          
          <div>
            <div class="d-flex align-items-center justify-content-between mb-2">
              <p class="fw-semibold mb-0">📂 Folders</p>
              <a href="#" class="mb-0 text-decoration-none" style="color: black;">New folder</a>
            </div>
            <div style="max-height: 250px; width: 260px; overflow-y: auto; scrollbar-width: thin; scrollbar-color: #888 #f1f1f1; border: 1px solid #ddd; padding: 8px; border-radius: 5px;">
              <ul class="nav flex-column" id="menu">
                <li v-for="(folder, index) in folders" :key="index">
                  <a href="#" class="nav-link d-flex align-items-center" @click="toggleFolder(index)">
                    <span>{{ folder.open ? '▼' : '▶' }}</span>
                    <i class="bi-folder-fill ms-2"></i> 
                    <span class="ms-2">{{ folder.name }}</span>
                  </a>
                  <ul v-show="folder.open" class="nav flex-column ps-4">
                    <li v-for="(subfolder, subIndex) in folder.subfolders" :key="subIndex" class="ps-0">
                      <a href="#" class="nav-link d-flex align-items-center">
                        <span class="ms-4">📁 {{ subfolder }}</span>
                      </a>
                    </li>
                  </ul>
                </li>
              </ul>
            </div>
          </div>
          
          <div class="mt-4">
            <p class="fw-semibold">👥 Members</p>
            <div class="form-check">
              <input type="checkbox" class="form-check-input" id="selectAll" v-model="selectAll" @change="toggleAll">
              <label for="selectAll" class="form-check-label">All Selected</label>
            </div>
            <div class="form-check">
              <input type="checkbox" class="form-check-input" id="admin" v-model="selectedMembers" value="Admin">
              <label for="admin" class="form-check-label">Admin</label>
            </div>
          </div>
        </aside>
        
        <!-- Main Content -->
        <main class="flex-grow-1 p-4">
          <table class="table table-hover table-bordered text-center align-middle">
            <thead class="table-light">
              <tr>
                <th><input type="checkbox" /></th>
                <th>Name</th>
                <th>Preview</th>
                <th>Dimension</th>
                <th>Size</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="image in images" :key="image.name">
                <td><input type="checkbox" /></td>
                <td>{{ image.name }}</td>
                <td>
                  <img :src="image.url" class="rounded shadow-sm" style="width: 50px; height: 50px; object-fit: cover;" />
                </td>
                <td>{{ image.dimension }}</td>
                <td>{{ image.size }}</td>
              </tr>
            </tbody>
          </table>
        </main>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, watch, onMounted } from 'vue';

export default {
  setup() {
    const images = ref([
      { name: 'Seasandiego.jpg', dimension: '2000 × 2000', size: '763.3 kB', url: 'https://via.placeholder.com/200' },
      { name: 'iMactwin.png', dimension: '2000 × 2000', size: '640.2 kB', url: 'https://via.placeholder.com/200' },
      { name: 'hustlecup.jpg', dimension: '2000 × 2000', size: '100.4 kB', url: 'https://via.placeholder.com/200' },
      { name: 'MS-Surface.jpg', dimension: '2000 × 2000', size: '113.2 kB', url: 'https://via.placeholder.com/200' },
      { name: 'Famoustower.jpg', dimension: '2000 × 2000', size: '547.2 kB', url: 'https://via.placeholder.com/200' }
    ]);

    const folders = ref([
      { name: 'Uploads', subfolders: ['Images (1)', 'Documents (2)', 'Videos (2)'], open: true },
      { name: 'Source', subfolders: ['test1', 'test2 (2)'], open: false },
      { name: 'Share', subfolders: ['test1', 'test2 (2)'], open: false }
    ]);

    const selectAll = ref(false);
    const selectedMembers = ref(['Admin']);

    const toggleFolder = (index) => {
      folders.value.forEach((folder, i) => {
        folder.open = i === index ? !folder.open : false;
      });
    };

    watch(selectAll, (newVal) => {
      selectedMembers.value = newVal ? ['Admin'] : [];
    });

    const toggleAll = () => {
      selectAll.value = selectedMembers.value.length > 0;
    };

    return { images, selectAll, selectedMembers, toggleAll, folders, toggleFolder };
  }
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
}
</style>
