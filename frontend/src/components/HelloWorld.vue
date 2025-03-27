<template>
  <div class="d-flex justify-content-center align-items-center bg-light">
    <div class="container bg-white shadow rounded p-4" style="max-width: 1200px; width: 100%; min-height: 80vh;">
      <div class="d-flex align-items-stretch">
        <!-- Sidebar -->
        <aside class="bg-white p-4" style="width:250px" >
          <button class="btn btn-primary w-100 py-3 mb-3">Import Documents</button>
          
          <div class="mb-3">
            <div class="nav-link d-flex align-items-center justify-content-between">
            <span class="folder-title">Storage</span>
            <a href="#" class="folder-name ms-2 text-decoration-underline">Change Plan</a>
            </div>
            <div class="progress mt-2">
              <div class="progress-bar bg-primary" style="width: 6%;"></div>
            </div>
            <p class="small text-muted mt-1">6% used of 2GB</p>
          </div>
          <!-- search -->
          <div class="input-group border rounded w-100 py-2 mb-3">
            <input type="text" class="form-control border-0 shadow-none" placeholder="Search...">
            <span class="input-group-text bg-white text-secondary border-0">
              <i class="bi bi-search"></i>
            </span>
          </div>
          <div class="nav-link d-flex align-items-center justify-content-between">
            <span class="folder-title">Folders</span>
            <a href="#" class="folder-name ms-2 text-decoration-underline">New folder</a>
          </div>

          <div style="max-height: 190px; width: 225px; overflow-y: auto; scrollbar-width: thin; scrollbar-color: #888 #f1f1f1; border-radius: 5px;">
            <ul class="nav flex-column" id="menu">
              <li v-for="(folder, index) in folders" :key="index">
                <a href="#" class="nav-link d-flex align-items-center p-0" @click="toggleFolder(index)">
                  <span class="folder-icon" :class="{ 'text-dark': folder.open }">{{ folder.open ? '▲' : '▶' }}</span>
                  <i class="bi-folder-fill ms-2" :class="{ 'text-dark': folder.open }"></i> 
                  <span class="folder-name ms-2" :class="{ 'text-dark': folder.open }">{{ folder.name }}</span>
                </a>

                <!-- Danh sách thư mục con (Cấp 1) -->
                <ul v-show="folder.open" class="nav flex-column ps-4">
                  <li v-for="(subfolder, subIndex) in folder.subfolders" :key="subIndex">
                    <a href="#" class="nav-link d-flex align-items-center" @click="toggleSubfolder(index, subIndex)">
                      <span class="folder-icon" :class="{ 'text-dark': subfolder.open }">{{ subfolder.open ? '▲' : '▶' }}</span>
                      <i class="bi-folder-fill ms-2" :class="{ 'text-dark': subfolder.open }"></i> 
                      <span class="folder-name ms-1" :class="{ 'text-dark': subfolder.open }"> {{ subfolder.name }} </span>
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
            <div class="nav-link d-flex align-items-center justify-content-between mb-3">
            <span class="folder-title">Member</span>
            <a href="#" class="folder-name ms-2 text-decoration-underline">Select All</a>
            </div>
            <div class="d-flex align-items-center mb-2">
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
                <th class="text-muted small text-start align-middle">
                  <input type="checkbox" />
                </th>
                <th class="text-start">
                  <span>Select All</span>
                </th>
                <th class="text-muted small text-start align-middle">
                    <div class="d-flex align-items-center">
                        <span>Name</span>
                        <span class="sort-icons ms-1">
                            <i class="bi bi-caret-up-fill"></i>
                            <i class="bi bi-caret-down-fill"></i>
                        </span>
                    </div>
                </th>
                <th class="text-muted small text-start align-middle">
                    <div class="d-flex align-items-center">
                        <span>Dimmension</span>
                        <span class="sort-icons ms-1">
                            <i class="bi bi-caret-up-fill"></i>
                            <i class="bi bi-caret-down-fill"></i>
                        </span>
                    </div>
                </th>
                <th class="text-muted small text-start align-middle">
                    <div class="d-flex align-items-center">
                        <span>Size</span>
                        <span class="sort-icons ms-1">
                            <i class="bi bi-caret-up-fill"></i>
                            <i class="bi bi-caret-down-fill"></i>
                        </span>
                    </div>
                </th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="image in images" :key="image.name">
                <td><input type="checkbox" /></td>
                <td class="row-img">
                  <img :src="image.url" class="rounded shadow-sm" style="width:225px; height: 100px; object-fit: cover;" />
                </td>
                <td class="text-start">{{ image.name }}</td>
                <td class="text-start">{{ image.dimmension }}</td>
                <td class="text-start">{{ image.size }}</td>
              </tr>
            </tbody>
          </table>
        </main>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue';

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
  const subfolder = folders.value[folderIndex]?.subfolders?.[subIndex];
  if (subfolder) {
    subfolder.open = !subfolder.open;
  }
};

// Đồng bộ trạng thái "Chọn tất cả"
watch(selectAll, (newVal) => {
  selectedMembers.value = newVal ? ['Admin'] : [];
});

const toggleAll = () => {
  selectAll.value = selectedMembers.value.length > 0;
};

const fetchData = async () => {
  try {
    const response = await fetch('./data.json'); 
    const data = await response.json();

    // Lọc danh sách hình ảnh từ "Uploads" > "Images"
    const uploadFolder = data.find(folder => folder.name === "Uploads");
    if (uploadFolder) {
      const imagesFolder = uploadFolder.children?.find(child => child.name === "Images");

      images.value = imagesFolder ? imagesFolder.children.map(img => ({
        name: img.name,
        url: img.url,
        dimmension: img.dimmension,
        size: img.size ? `${(parseInt(img.size) / 1024).toFixed(1)} kB` : 'N/A'
      })) : [];
    } else {
      images.value = [];
    }

    // Cập nhật danh sách thư mục
    folders.value = data.map((folder, index) => ({
    name: folder.name,
    subfolders: folder.children || [],
    open: index === 0 // Mở thư mục đầu tiên (index === 0)
    }));
  } catch (error) {
    console.error('Lỗi khi tải dữ liệu:', error);
  }
};

onMounted(fetchData);
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
  padding: 15px;
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
.table td {
  font-size: 14px;
}
.row-img{
  width: 150px !important;
  height: 80px !important;
}

.folder-name {
    color: #919090; /* Màu xám đậm hơn nhưng không quá đậm */
    font-weight: 400;
    font-size: 14px;
  }

  /* Thu nhỏ icon tam giác */
  .folder-icon {
    font-size: 14px;
    width: 12px;
    display: inline-block;
    text-align: center;
    color: #919090;
  }
  .bi-folder-fill {
  font-size: 16px;  /* Điều chỉnh kích thước */
  margin-right: 5px;
  color: #919090;
}
.folder-title{
  color: #919090;
}
th {
    white-space: nowrap; /* Đảm bảo tiêu đề không bị xuống dòng */
    padding: 12px 15px; /* Giữ khoảng cách đồng đều */
    vertical-align: middle; /* Căn giữa theo chiều dọc */
}

.d-flex {
    display: flex;
    align-items: center;
}

.sort-icons {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    font-size: 12px; /* Giảm kích thước icon */
    line-height: 1; /* Giữ icon gần nhau */
}

.sort-icons i {
    height: 5px;
    cursor: pointer;
    color: gray; /* Màu mặc định */
}

.sort-icons i:hover {
    color: black; /* Màu đậm khi hover */
}

.form-check-input {
    margin: 0; /* Đảm bảo checkbox không bị lệch */
    transform: scale(1.2); /* Làm checkbox lớn hơn một chút */
}
.bi-caret-up-fill{
  font-size: 7px;
}
.bi-caret-down-fill{
  font-size: 7px;
}

.table-light span{
  color: #919090;
  font-weight: 400 !important;
}

</style>
