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
          <div class="nav-link d-flex align-items-center justify-content-between">
            <span class="folder-title">Folders</span>
            <a href="#" class="folder-name ms-2 text-decoration-underline">New folder</a>
          </div>

          <div style="max-height: 250px; width: 260px; overflow-y: auto; scrollbar-width: thin; scrollbar-color: #888 #f1f1f1; border: 1px solid #ddd; padding: 8px; border-radius: 5px;">
            <ul class="nav flex-column" id="menu">
              <li v-for="(folder, index) in folders" :key="index">
                <a href="#" class="nav-link d-flex align-items-center" @click="toggleFolder(index)">
                  <span class="folder-icon" :class="folder.open ? 'text-dark' : 'text-secondary'">{{ folder.open ? '▲' : '▶' }}</span>
                  <i class="bi-folder-fill ms-2" :class="folder.open ? 'text-dark' : 'text-secondary'"></i> 
                  <span class="folder-name ms-2">{{ folder.name }}</span>
                </a>

                <!-- Danh sách thư mục con (Cấp 1) -->
                <ul v-show="folder.open" class="nav flex-column ps-4">
                  <li v-for="(subfolder, subIndex) in folder.subfolders" :key="subIndex">
                    <a href="#" class="nav-link d-flex align-items-center" @click="toggleSubfolder(index, subIndex)">
                      <span class="folder-icon" :class="subfolder.open ? 'text-dark' : 'text-secondary'">{{ subfolder.open ? '▲' : '▶' }}</span>
                      <i class="bi-folder-fill ms-2" :class="subfolder.open ? 'text-dark' : 'text-secondary'"></i> 
                      <span class="folder-name ms-1"> {{ subfolder.name }} </span>
                    </a>

                    <!-- Danh sách thư mục con (Cấp 2) -->
                    <ul v-show="subfolder.open" class="nav flex-column ps-4">
                      <li v-for="(child, childIndex) in subfolder.children" :key="childIndex">
                        <a href="#" class="nav-link d-flex align-items-center">
                          <span class="folder-name ms-2"><i class="bi bi-image"></i> {{ child.name }}</span>
                        </a>
                      </li>
                    </ul>
                  </li>
                </ul>
              </li>
            </ul>
          </div>      
          
          <div class="mt-4">
            <div class="nav-link d-flex align-items-center justify-content-between">
            <span class="folder-title">Member</span>
            <a href="#" class="folder-name ms-2 text-decoration-underline">Select All</a>
          </div>
            <div class="d-flex align-items-center">
              <input type="checkbox" class="form-check-input me-2" id="selectAll" v-model="selectAll" @change="toggleAll">
              <label for="selectAll" class="form-check-label">All Selected</label>
            </div>

            <div class="d-flex align-items-center">
                <input type="checkbox" class="form-check-input me-2" id="admin" v-model="selectedMembers" value="Admin">
                <label for="selectAll" class="form-check-label">Admin</label>
            </div>
          </div>
        </aside>
        
        <!-- Main Content -->
        <main class="flex-grow-1 p-4">
          <table class="table table-hover text-center align-middle custom-table">
            <thead class="table-light">
              <tr>
                <th class="text-muted small text-start"><input type="checkbox" /></th>
                <th class="text-muted small text-start">Select All</th>
                <th class="text-muted small text-start">Name</th>
                <th class="text-muted small text-start">dimmension</th>
                <th class="text-muted small text-start">Size</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="image in images" :key="image.name">
                <td><input type="checkbox" /></td>
                <td class="row-img">
                  <img :src="image.url" class="rounded shadow-sm" style="width:200px; height: 80px; object-fit: cover;" />
                </td>
                <td class="text-start">{{ image.name }}</td>
                <td>{{ image.dimmension }}</td>
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
    const images = ref([]);
    const folders = ref([]);
    const selectAll = ref(false);
    const selectedMembers = ref(['Admin']);

    const toggleFolder = (index) => {
      folders.value.forEach((folder, i) => {
        folder.open = i === index ? !folder.open : false;
      });
    };

    const toggleSubfolder = (folderIndex, subIndex) => {
    const subfolder = folders.value[folderIndex].subfolders[subIndex];
    if (subfolder) {
      subfolder.open = !subfolder.open;
    }
  }

    // Đồng bộ trạng thái "Chọn tất cả"
    watch(selectAll, (newVal) => {
      selectedMembers.value = newVal ? ['Admin'] : [];
    });

    const toggleAll = () => {
      selectAll.value = selectedMembers.value.length > 0;
    };

    return { images, selectAll, selectedMembers, toggleAll, folders, toggleFolder, toggleSubfolder };
  },

  methods: {
    async fetchData() {
      try {
        const response = await fetch('./data.json'); 
        const data = await response.json();
        // Lọc ra danh sách hình ảnh từ "Uploads" > "Images"
        const uploadFolder = data.find(folder => folder.name === "Uploads");
        if (uploadFolder) {
          const imagesFolder = uploadFolder.children.find(child => child.name === "Images");
          this.images = imagesFolder ? imagesFolder.children.map(img => ({
            name: img.name,
            url: img.url,
            dimmension: img.dimmension,
            size: `${(parseInt(img.size) / 1024).toFixed(1)} kB`
          })) : [];
        }

        // Cập nhật danh sách thư mục
        this.folders = data.map(folder => ({
          name: folder.name,
          subfolders: folder.children,
          open: false
        }));
      } catch (error) {
        // console.error('Lỗi khi tải dữ liệu:', error);
      }
    }
  },

  mounted() {
    this.fetchData();
  }
};
</script>


<style>
body {
  font-family: Arial, sans-serif;
}
.custom-table {
  border-collapse: collapse;
}

.custom-table th, 
.custom-table td {
  border: none !important; /* Viền mờ */
}

/* Làm viền bảng mờ hơn */
.custom-table thead {
  border-bottom: none; /* Mờ hơn bình thường */
}

/* Tiêu đề cột nhạt và nhỏ */
.custom-table th {
  font-size: 14px;
  color: #6c757d; /* Bootstrap text-muted */
}
.row-img{
  width: 150px !important;
  height: 80px !important;
}

.folder-name {
    color: #444; /* Màu xám đậm hơn nhưng không quá đậm */
    font-weight: 500;
  }

  /* Thu nhỏ icon tam giác */
  .folder-icon {
    font-size: 12px;
    width: 12px;
    display: inline-block;
    text-align: center;
  }
  .bi-folder-fill {
  font-size: 16px;  /* Điều chỉnh kích thước */
  margin-right: 5px;
}
</style>
