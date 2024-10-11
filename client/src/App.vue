<template>
  <div>
    <ListUser />
    <br />
    <UserDetail />
    <br />
    <div class="container-xl">
      <div class="table-responsive">
        <div class="table-wrapper">
          <div class="table-title">
            <div class="row">
              <div class="col-sm-6">
                <h2>Quản lý <b>sinh viên</b></h2>
              </div>
              <div class="col-sm-6">
                <button
                  class="btn btn-success"
                  data-toggle="modal"
                  @click="handleShowFormAdd"
                >
                  <i class="material-icons">&#xE147;</i>
                  <span>Thêm mới sinh viên</span>
                </button>
              </div>
            </div>
          </div>
          <table class="table table-striped table-hover">
            <thead>
              <tr>
                <th>
                  <span class="custom-checkbox">
                    <input type="checkbox" id="selectAll" />
                    <label for="selectAll"></label>
                  </span>
                </th>
                <th>Tên sinh viên</th>
                <th>Email</th>
                <th>Địc chỉ</th>
                <th>Số điện thoại</th>
                <th>Lựa chọn</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="user in paginatedUsers" :key="user.id">
                <td>
                  <span class="custom-checkbox">
                    <input
                      type="checkbox"
                      id="checkbox5"
                      name="options[]"
                      value="1"
                    />
                    <label for="checkbox5"></label>
                  </span>
                </td>
                <td>{{ user.student_name }}</td>
                <td>{{ user.email }}</td>
                <td>{{ user.address }}</td>
                <td>{{ user.phone }}</td>
                <td>
                  <a
                    class="edit"
                    data-toggle="modal"
                    @click="handleShowFormEdit(user)"
                    ><i
                      class="material-icons"
                      data-toggle="tooltip"
                      title="Edit"
                      >&#xE254;</i
                    >
                  </a>
                  <a
                    class="delete"
                    data-toggle="modal"
                    @click="handleShowFormDelete(user.id)"
                    ><i
                      class="material-icons"
                      data-toggle="tooltip"
                      title="Delete"
                      >&#xE872;</i
                    >
                  </a>
                </td>
              </tr>
            </tbody>
          </table>
          <div class="clearfix">
            <div class="hint-text">
              Hiển thị
              <b>{{ currentPage * itemsPerPage - itemsPerPage + 1 }}</b> đến
              <b>{{ Math.min(currentPage * itemsPerPage, users.length) }}</b>
              bản ghi
            </div>
            <ul class="pagination">
              <li class="page-item" :class="{ disabled: currentPage === 1 }">
                <a href="#" @click.prevent="changePage(currentPage - 1)"
                  >Trước</a
                >
              </li>
              <li
                v-for="page in totalPages"
                :key="page"
                class="page-item"
                :class="{ active: page === currentPage }"
              >
                <a
                  href="#"
                  @click.prevent="changePage(page)"
                  class="page-link"
                  >{{ page }}</a
                >
              </li>
              <li
                class="page-item"
                :class="{ disabled: currentPage === totalPages }"
              >
                <a href="#" @click.prevent="changePage(currentPage + 1)">Sau</a>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
    <!-- Edit Modal HTML -->
    <teleport to="body">
      <div id="addEmployeeModal" v-if="isShowForm">
        <div class="modal-dialog">
          <div class="modal-content">
            <form @submit.prevent="handleAdd">
              <div class="modal-header">
                <h4 class="modal-title">Thêm mới sinh viên</h4>
                <button
                  type="button"
                  class="close"
                  data-dismiss="modal"
                  aria-hidden="true"
                >
                  &times;
                </button>
              </div>
              <div class="modal-body">
                <div class="form-group">
                  <label>Tên sinh viên</label>
                  <input
                    type="text"
                    class="form-control"
                    v-model="inputValue.student_name"
                  />
                  <p style="color: red">{{ error.student_name }}</p>
                </div>
                <div class="form-group">
                  <label>Email</label>
                  <input
                    type="text"
                    class="form-control"
                    v-model="inputValue.email"
                  />
                  <p style="color: red">{{ error.email }}</p>
                </div>
                <div class="form-group">
                  <label>Địa chỉ</label>
                  <textarea
                    class="form-control"
                    v-model="inputValue.address"
                  ></textarea>
                </div>
                <div class="form-group">
                  <label>Số điện thoại</label>
                  <input
                    type="text"
                    class="form-control"
                    v-model="inputValue.phone"
                  />
                  <p style="color: red">{{ error.phone }}</p>
                </div>
              </div>
              <div class="modal-footer">
                <button
                  type="button"
                  class="btn btn-default"
                  data-dismiss="modal"
                  @click="handleCloseFormAdd"
                >
                  Huy
                </button>
                <button type="submit" class="btn btn-success">Thêm</button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </teleport>

    <!-- Edit Modal HTML -->
    <teleport to="body">
      <div id="editEmployeeModal" v-if="showFormEdit">
        <div class="modal-dialog">
          <div class="modal-content">
            <form>
              <div class="modal-header">
                <h4 class="modal-title">Sửa thông tin sinh viên</h4>
                <button
                  type="button"
                  class="close"
                  data-dismiss="modal"
                  aria-hidden="true"
                >
                  &times;
                </button>
              </div>
              <div class="modal-body">
                <div class="form-group">
                  <label>Tên sinh viên</label>
                  <input
                    type="text"
                    class="form-control"
                    v-model="editUser.student_name"
                  />
                  <p style="color: red">{{ error.student_name }}</p>
                </div>
                <div class="form-group">
                  <label>Email</label>
                  <input
                    type="email"
                    class="form-control"
                    v-model="editUser.email"
                  />
                  <p style="color: red">{{ error.email }}</p>
                </div>
                <div class="form-group">
                  <label>Địa chỉ</label>
                  <textarea
                    class="form-control"
                    v-model="editUser.address"
                  ></textarea>
                </div>
                <div class="form-group">
                  <label>Số điện thoại</label>
                  <input
                    type="text"
                    class="form-control"
                    v-model="editUser.phone"
                  />
                  <p style="color: red">{{ error.phone }}</p>
                </div>
              </div>
              <div class="modal-footer">
                <button
                  type="button"
                  class="btn btn-default"
                  data-dismiss="modal"
                  @click="handleCloseFormEdit"
                >
                  Hủy
                </button>
                <button
                  type="button"
                  class="btn btn-info"
                  @click="handleUpdate"
                >
                  Cập nhật
                </button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </teleport>
    <!-- Delete Modal HTML -->
    <teleport to="body">
      <div id="deleteEmployeeModal" v-if="isShowFormDelete">
        <div class="modal-dialog">
          <div class="modal-content">
            <form>
              <div class="modal-header">
                <h4 class="modal-title">Xóa nhân viên</h4>
                <button
                  type="button"
                  class="close"
                  data-dismiss="modal"
                  aria-hidden="true"
                >
                  &times;
                </button>
              </div>
              <div class="modal-body">
                <p>Bạn chắc chắn muốn xóa sinh viên này?</p>
              </div>
              <div class="modal-footer">
                <button
                  type="button"
                  class="btn btn-default"
                  data-dismiss="modal"
                  @click="hanldeCloseFormDelete"
                >
                  Hủy
                </button>
                <button
                  type="button"
                  class="btn btn-danger"
                  @click="handleDelete"
                >
                  Xóa
                </button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </teleport>
  </div>
</template>
<script setup>
import { computed, onMounted, reactive, ref } from "vue";
import ListUser from "./components/ListUser.vue";
import UserDetail from "./components/UserDetail.vue";
import axios from "axios";

const users = ref([]);
const isShowForm = ref(false);
const showFormEdit = ref(false);
const isShowFormDelete = ref(false);
const editUser = ref(null);
const deleteUser = ref(null);

// Thêm các state cho phân trang
const currentPage = ref(1);
const itemsPerPage = ref(5);
const inputValue = reactive({
  student_name: "",
  email: "",
  phone: "",
  address: "",
});

const error = reactive({
  student_name: "",
  email: "",
  phone: "",
  address: "",
});
const getAllUsers = async () => {
  const response = await axios.get("http://localhost:8080/users");
  users.value = response.data;
};

onMounted(() => {
  getAllUsers();
});

// Hàm thêm sinh viên
const handleShowFormAdd = () => {
  isShowForm.value = true;
};

const handleCloseFormAdd = () => {
  isShowForm.value = false;
};

const reset = () => {
  inputValue.student_name = "";
  inputValue.email = "";
  inputValue.phone = "";
  inputValue.address = "";
};

const handleAdd = async () => {
  const existEmail = await axios.get(
    `http://localhost:8080/users?email_like=${inputValue.email}`
  );
  console.log(11111, existEmail);

  if (!inputValue.student_name) {
    error.student_name = "Vui lòng nhập tên sinh viên";
  } else {
    error.student_name = "";
  }

  if (!inputValue.email) {
    error.email = "Vui lòng nhập email";
  } else if (existEmail.data.length !== 0) {
    error.email = "Email đã tồn tại";
  } else {
    error.email = "";
  }

  if (!inputValue.phone) {
    error.phone = "Vui lòng nhập số điện thoại";
  } else {
    error.phone = "";
  }

  if (!error.student_name && !error.email && !error.phone) {
    const newUser = {
      student_name: inputValue.student_name,
      email: inputValue.email,
      phone: inputValue.phone,
      address: inputValue.address,
      status: false,
    };
    await axios.post("http://localhost:8080/users", newUser);
    getAllUsers();
    reset();
    handleCloseFormAdd();
  }
};

// Hàm xóa nhân viên
const handleShowFormDelete = (userId) => {
  deleteUser.value = userId;
  isShowFormDelete.value = true;
};

const hanldeCloseFormDelete = () => {
  isShowFormDelete.value = false;
};

const handleDelete = async () => {
  await axios.delete(`http://localhost:8080/users/${deleteUser.value}`);
  getAllUsers();
  hanldeCloseFormDelete();
};

// Hàm cập nhật sinh viên
const handleShowFormEdit = (user) => {
  editUser.value = user;
  showFormEdit.value = true;
};

const handleCloseFormEdit = () => {
  showFormEdit.value = false;
};

const handleUpdate = async () => {
  if (!editUser.value.student_name) {
    error.student_name = "Vui lòng nhập tên sinh viên";
  } else {
    error.student_name = "";
  }

  if (!editUser.value.email) {
    error.email = "Vui lòng nhập email";
  } else {
    error.email = "";
  }

  if (!editUser.value.phone) {
    error.phone = "Vui lòng nhập số điện thoại";
  } else {
    error.phone = "";
  }

  if (!error.student_name && !error.email && !error.phone) {
    const updateUser = {
      id: editUser.value.id,
      ...editUser.value,
    };
    await axios.put(
      `http://localhost:8080/users/${editUser.value.id}`,
      updateUser
    );
    getAllUsers();
    handleCloseFormEdit();
  }
};

// Hàm phân trang
// Tính tổng số trang
const totalPages = computed(() => {
  return Math.ceil(users.value.length / itemsPerPage.value);
});

// Phân trang: Lấy dữ liệu sinh viên của trang hiện tại
const paginatedUsers = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage.value;
  const end = start + itemsPerPage.value;
  return users.value.slice(start, end);
});

// Thay đổi trang
const changePage = (page) => {
  if (page < 1 || page > totalPages.value) return;
  currentPage.value = page;
};
</script>
<style scoped>
body {
  color: #566787;
  background: #f5f5f5;
  font-family: "Varela Round", sans-serif;
  font-size: 13px;
}
.table-responsive {
  margin: 30px 0;
}
.table-wrapper {
  background: #fff;
  padding: 20px 25px;
  border-radius: 3px;
  min-width: 1000px;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.05);
}
.table-title {
  padding-bottom: 15px;
  background: #435d7d;
  color: #fff;
  padding: 16px 30px;
  min-width: 100%;
  margin: -20px -25px 10px;
  border-radius: 3px 3px 0 0;
}
.table-title h2 {
  margin: 5px 0 0;
  font-size: 24px;
}
.table-title .btn-group {
  float: right;
}
.table-title .btn {
  color: #fff;
  float: right;
  font-size: 13px;
  border: none;
  min-width: 50px;
  border-radius: 2px;
  border: none;
  outline: none !important;
  margin-left: 10px;
}
.table-title .btn i {
  float: left;
  font-size: 21px;
  margin-right: 5px;
}
.table-title .btn span {
  float: left;
  margin-top: 2px;
}
table.table tr th,
table.table tr td {
  border-color: #e9e9e9;
  padding: 12px 15px;
  vertical-align: middle;
}
table.table tr th:first-child {
  width: 60px;
}
table.table tr th:last-child {
  width: 100px;
}
table.table-striped tbody tr:nth-of-type(odd) {
  background-color: #fcfcfc;
}
table.table-striped.table-hover tbody tr:hover {
  background: #f5f5f5;
}
table.table th i {
  font-size: 13px;
  margin: 0 5px;
  cursor: pointer;
}
table.table td:last-child i {
  opacity: 0.9;
  font-size: 22px;
  margin: 0 5px;
}
table.table td a {
  font-weight: bold;
  color: #566787;
  display: inline-block;
  text-decoration: none;
  outline: none !important;
}
table.table td a:hover {
  color: #2196f3;
}
table.table td a.edit {
  color: #ffc107;
}
table.table td a.delete {
  color: #f44336;
}
table.table td i {
  font-size: 19px;
}
table.table .avatar {
  border-radius: 50%;
  vertical-align: middle;
  margin-right: 10px;
}
.pagination {
  float: right;
  margin: 0 0 5px;
}
.pagination li a {
  border: none;
  font-size: 13px;
  min-width: 30px;
  min-height: 30px;
  color: #999;
  margin: 0 2px;
  line-height: 30px;
  border-radius: 2px !important;
  text-align: center;
  padding: 0 6px;
}
.pagination li a:hover {
  color: #666;
}
.pagination li.active a,
.pagination li.active a.page-link {
  background: #03a9f4;
}
.pagination li.active a:hover {
  background: #0397d6;
}
.pagination li.disabled i {
  color: #ccc;
}
.pagination li i {
  font-size: 16px;
  padding-top: 6px;
}
.hint-text {
  float: left;
  margin-top: 10px;
  font-size: 13px;
}
/* Custom checkbox */
.custom-checkbox {
  position: relative;
}
.custom-checkbox input[type="checkbox"] {
  opacity: 0;
  position: absolute;
  margin: 5px 0 0 3px;
  z-index: 9;
}
.custom-checkbox label:before {
  width: 18px;
  height: 18px;
}
.custom-checkbox label:before {
  content: "";
  margin-right: 10px;
  display: inline-block;
  vertical-align: text-top;
  background: white;
  border: 1px solid #bbb;
  border-radius: 2px;
  box-sizing: border-box;
  z-index: 2;
}
.custom-checkbox input[type="checkbox"]:checked + label:after {
  content: "";
  position: absolute;
  left: 6px;
  top: 3px;
  width: 6px;
  height: 11px;
  border: solid #000;
  border-width: 0 3px 3px 0;
  transform: inherit;
  z-index: 3;
  transform: rotateZ(45deg);
}
.custom-checkbox input[type="checkbox"]:checked + label:before {
  border-color: #03a9f4;
  background: #03a9f4;
}
.custom-checkbox input[type="checkbox"]:checked + label:after {
  border-color: #fff;
}
.custom-checkbox input[type="checkbox"]:disabled + label:before {
  color: #b8b8b8;
  cursor: auto;
  box-shadow: none;
  background: #ddd;
}
/* Modal styles */
#addEmployeeModal,
#deleteEmployeeModal,
#editEmployeeModal {
  width: 100%;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #0000002c;
}

.modal .modal-dialog {
  width: 600px;
  /* max-width: 600px; */
}
.modal .modal-header,
.modal .modal-body,
.modal .modal-footer {
  padding: 20px 30px;
}
.modal .modal-content {
  width: 600px;
  border-radius: 3px;
  font-size: 14px;
}
.modal .modal-footer {
  background: #ecf0f1;
  border-radius: 0 0 3px 3px;
}
.modal .modal-title {
  display: inline-block;
}
.modal .form-control {
  border-radius: 2px;
  box-shadow: none;
  border-color: #dddddd;
}
.modal textarea.form-control {
  resize: vertical;
}
.modal .btn {
  border-radius: 2px;
  min-width: 100px;
}
.modal form label {
  font-weight: normal;
}
</style>
