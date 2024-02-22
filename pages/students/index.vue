<template>
  <div class="container">
    <h1>শিক্ষার্থীদের তালিকা</h1>
    <nuxt-link to="./create-student.html" class="primary-btn"
      >রেজিষ্ট্রেশন
    </nuxt-link>

    <h5>
      সর্বমোট {{ convertToBengaliNumber(totalStudents) }} জন শিক্ষার্থীর তথ্য
      রয়েছে
    </h5>
    <div class="d-flex gap-3 mb-4 justify-content-between">
      <input
        type="text"
        v-model="searchQuery"
        placeholder="Search by name..."
        class="form-control shadow-none"
      />

      <select v-model="genderFilter" class="form-select shadow-none">
        <option value="">All Genders</option>
        <option value="Male">Male</option>
        <option value="Female">Female</option>
        <option value="Other">Other</option>
      </select>
      <!-- Inside the existing <div class="d-flex gap-3 mb-4 justify-content-between"> ... </div> -->
      <select v-model="classFilter" class="form-select shadow-none">
        <option value="">All Classes</option>
        <option
          v-for="className in classNames"
          :key="className"
          :value="className._id"
        >
          {{ className.className }}
        </option>
      </select>
    </div>

    <ul v-if="!isContentLoading" class="list-group mb-3">
      <li
        class="list-group-item d-flex justify-content-between align-items-center"
        v-for="student in students"
        :key="student._id"
      >
        <span>
          {{ student.fullName }} - {{ student.studentClass.className }} -
          <span
            data-bs-toggle="modal"
            data-bs-target="#setSalleryModal"
            @click="showModal(student)"
          >
            <b>বেতন :</b> <span>{{ student.monthlyFee }}</span>
          </span>
        </span>
        <div class="d-flex gap-4 align-items-center">
          <span
            @click="showModal(student)"
            data-bs-toggle="modal"
            data-bs-target="#myModal"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="16"
              height="16"
              fill="currentColor"
              class="bi bi-info-circle-fill"
              viewBox="0 0 16 16"
            >
              <path
                d="M8 16A8 8 0 1 0 8 0a8 8 0 0 0 0 16m.93-9.412-1 4.705c-.07.34.029.533.304.533.194 0 .487-.07.686-.246l-.088.416c-.287.346-.92.598-1.465.598-.703 0-1.002-.422-.808-1.319l.738-3.468c.064-.293.006-.399-.287-.47l-.451-.081.082-.381 2.29-.287zM8 5.5a1 1 0 1 1 0-2 1 1 0 0 1 0 2"
              />
            </svg>
          </span>
          <span
            class="display-5"
            @click="showModal(student)"
            data-bs-toggle="modal"
            data-bs-target="#paymentModal"
          >
            ৳
          </span>
        </div>
      </li>
    </ul>
    <div v-else class="">
      <div class="list-group mb-3">
        <div
          class="list-group-item d-flex justify-content-between align-items-center skeleton-item"
          v-for="n in 5"
          :key="n"
        >
          <div class="skeleton-text skeleton-animation"></div>
          <div class="d-flex gap-4 align-items-center">
            <div class="skeleton-icon skeleton-animation"></div>
            <div class="skeleton-currency skeleton-animation"></div>
          </div>
        </div>
      </div>
    </div>

    <nav aria-label="Page navigation">
      <ul class="pagination">
        <li class="page-item" :class="{ disabled: currentPage === 1 }">
          <a class="page-link shadow-none" href="#" @click.prevent="prevPage"
            >Previous</a
          >
        </li>
        <li
          class="page-item"
          v-for="page in displayedPages"
          :key="page"
          :class="{ active: currentPage === page }"
        >
          <a
            class="page-link shadow-none"
            href="#"
            @click.prevent="setPage(page)"
            >{{ page }}</a
          >
        </li>
        <li class="page-item" :class="{ disabled: currentPage === totalPages }">
          <a class="page-link shadow-none" href="#" @click.prevent="nextPage"
            >Next</a
          >
        </li>
      </ul>
    </nav>

    <!-- student fetails -->
    <div
      class="modal fade"
      id="myModal"
      tabindex="-1"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">
              {{ selectedStudent.fullName }} এর বিস্তারিতঃ
            </h5>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <div class="img-wrapper">
              <img :src="selectedStudent.studentImage" alt="" />
            </div>
            <ul class="list-group">
              <li class="list-group-item d-flex justify-content-between">
                <span>নামঃ</span>
                <span>{{ selectedStudent.fullName }}</span>
              </li>
              <li class="list-group-item d-flex justify-content-between">
                <span>ক্লাসঃ</span>
                <span>{{ selectedStudent.studentClass?.className }}</span>
              </li>
              <li class="list-group-item d-flex justify-content-between">
                <span>মোবাইলঃ</span>
                <span>{{ selectedStudent.phone }}</span>
              </li>
              <li class="list-group-item d-flex justify-content-between">
                <span>জন্ম তারিখ</span>
                <span>{{ formatDate(selectedStudent?.dateOfBirth) }}</span>
              </li>
              <li class="list-group-item d-flex justify-content-between">
                <span>গ্রাম</span>
                <span>{{ selectedStudent?.address?.village }}</span>
              </li>
              <li class="list-group-item d-flex justify-content-between">
                <span>ইউনিয়ন</span>
                <span>{{ selectedStudent?.address?.union }}</span>
              </li>
              <li class="list-group-item d-flex justify-content-between">
                <span>থানা</span>
                <span>{{ selectedStudent?.address?.upazila }}</span>
              </li>
              <li class="list-group-item d-flex justify-content-between">
                <span>জেলা</span>
                <span>{{ selectedStudent?.address?.district }}</span>
              </li>
              <li class="list-group-item d-flex justify-content-between">
                <span>লিঙ্গ</span>
                <span>{{
                  selectedStudent?.gender === "Male" ? "পুরুষ" : "মহিলা"
                }}</span>
              </li>
            </ul>

            <!-- Modal content goes here -->
            <div class="table-responsive">
              <table class="table table-striped table-bordered">
                <thead>
                  <tr>
                    <th>Month</th>
                    <th>Year</th>
                    <th>Amount</th>
                    <th>Paid</th>
                    <th>Discount</th>
                    <th>Payment Method</th>
                    <th>Received By</th>
                    <th>Payment Date</th>
                    <th>Transaction ID</th>
                    <th>Remarks</th>
                  </tr>
                </thead>
                <tbody>
                  <tr
                    v-for="payment in selectedStudent.payments"
                    :key="payment._id"
                  >
                    <td>{{ payment.month }}</td>
                    <td>{{ payment.year }}</td>
                    <td>{{ payment.amount }}</td>
                    <td>{{ payment.paid }}</td>
                    <td>{{ payment.discount }}</td>
                    <td>{{ payment.paymentMethod }}</td>
                    <td>{{ payment.receivedBy }}</td>
                    <td>{{ payment.paymentDate }}</td>
                    <td>{{ payment.transactionID }}</td>
                    <td>{{ payment.remarks }}</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
              data-bs-dismiss="modal"
            >
              Close
            </button>
          </div>
        </div>
      </div>
    </div>
    <!-- set payment  -->
    <div
      class="modal fade"
      id="setSalleryModal"
      tabindex="-1"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
      data-bs-backdrop="static"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel"></h5>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
              @click="clickCancel"
            ></button>
          </div>
          <div class="modal-body">
            <div>
              {{ selectedStudent.monthlyFeea }}
              <input type="text" v-model="selectedStudent.monthlyFee" />
            </div>

            <!-- Modal content goes here -->
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
              data-bs-dismiss="modal"
              @click="clickCancel"
            >
              Close
            </button>
            <button
              type="button"
              class="btn btn-primary"
              @click="saveMonthlyFee"
            >
              Save
            </button>
          </div>
        </div>
      </div>
    </div>

    <div
      class="modal fade"
      id="paymentModal"
      tabindex="-1"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">
              Modal title {{ selectedStudent._id }}
            </h5>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <!-- Modal content goes here -->
            <div class="row justify-content-center">
              <div class="col-md-6">
                <form @submit.prevent="submitPayment">
                  <div class="form-group">
                    <label for="student">Student ID</label>
                    <input
                      type="text"
                      class="form-control"
                      id="student"
                      :value="selectedStudent?._id"
                    />
                  </div>
                  <div class="form-group">
                    <label for="month">Month</label>
                    <select
                      class="form-control"
                      id="month"
                      v-model="payment.month"
                    >
                      <option value="">Select Month</option>
                      <option
                        v-for="month in months"
                        :key="month"
                        :value="month"
                      >
                        {{ month }}
                      </option>
                    </select>
                  </div>
                  <div class="form-group">
                    <label for="year">Year</label>
                    <input
                      type="number"
                      class="form-control"
                      id="year"
                      v-model="payment.year"
                    />
                  </div>
                  <!-- ... Existing HTML code for the form and Vue instance ... -->

                  <div class="form-group">
                    <label for="amount">Amount</label>
                    <input
                      type="number"
                      class="form-control"
                      id="amount"
                      v-model="payment.amount"
                    />
                  </div>

                  <div class="form-group">
                    <label for="paid">Paid</label>
                    <input
                      type="checkbox"
                      class="form-control"
                      id="paid"
                      v-model="payment.paid"
                    />
                  </div>

                  <div class="form-group">
                    <label for="discount">Discount</label>
                    <input
                      type="number"
                      class="form-control"
                      id="discount"
                      v-model="payment.discount"
                    />
                  </div>

                  <div class="form-group">
                    <label for="paymentMethod">Payment Method</label>
                    <select
                      class="form-control"
                      id="paymentMethod"
                      v-model="payment.paymentMethod"
                    >
                      <option value="">Select Payment Method</option>
                      <option value="cash">Cash</option>
                      <option value="credit_card">Credit Card</option>
                      <!-- Add other payment methods as options -->
                    </select>
                  </div>

                  <div class="form-group">
                    <label for="receivedBy">Received By (Teacher ID)</label>
                    <input
                      type="text"
                      class="form-control"
                      id="receivedBy"
                      v-model="payment.receivedBy"
                    />
                  </div>

                  <div class="form-group">
                    <label for="paymentDate">Payment Date</label>
                    <input
                      type="date"
                      class="form-control"
                      id="paymentDate"
                      v-model="payment.paymentDate"
                    />
                  </div>

                  <div class="form-group">
                    <label for="transactionID">Transaction ID</label>
                    <input
                      type="text"
                      class="form-control"
                      id="transactionID"
                      v-model="payment.transactionID"
                    />
                  </div>

                  <div class="form-group">
                    <label for="remarks">Remarks</label>
                    <textarea
                      class="form-control"
                      id="remarks"
                      v-model="payment.remarks"
                    ></textarea>
                  </div>

                  <!-- ... Submit button and closing tags ... -->

                  <button type="submit" class="btn btn-primary">
                    Submit Payment
                  </button>
                </form>
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
              data-bs-dismiss="modal"
            >
              Close
            </button>
            <button type="button" class="btn btn-primary">Save changes</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template> 
<script>
export default {
  data() {
    return {
      students: [],
      isContentLoading: true,
      selectedStudent: {},
      currentPage: 1,
      totalPages: 1,
      totalStudents: 0,
      limit: 5, // Adjust this value based on your pagination limit
      searchQuery: "",
      genderFilter: "",
      months: [
        // Array of months for dropdown options
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ],
      classFilter: "",
      classNames: null,

      payment: {
        student: this.selectedStudent?._id,
        month: "", // Initially empty
        year: 2023,
        amount: 100,
        paid: true,
        discount: 10,
        paymentMethod: "cash",
        receivedBy: "655ab06a29081ca4fb2332c2",
        paymentDate: "2023-01-01",
        transactionID: "transaction_id_here",
        remarks: "Some remarks here",
        // Add other properties here and bind them to the respective form fields
      },
    };
  },
  computed: {
    displayedPages() {
      const totalVisiblePages = 5; // Adjust this value to change the number of visible pages
      let startPage = this.currentPage - Math.floor(totalVisiblePages / 2);
      startPage = Math.max(1, startPage);
      let endPage = startPage + totalVisiblePages - 1;
      endPage = Math.min(this.totalPages, endPage);

      const pages = [];
      for (let i = startPage; i <= endPage; i++) {
        pages.push(i);
      }

      return pages;
    },
  },
  mounted() {
    this.fetchClassess();
    this.fetchData();
  },
  watch: {
    searchQuery() {
      this.currentPage = 1; // Reset to the first page when search changes
      this.fetchData();
    },
    genderFilter() {
      this.currentPage = 1;
      this.fetchData();
    },

    classFilter() {
      this.currentPage = 1;
      this.fetchData();
    },
  },

  methods: {
    async fetchData() {
      this.isContentLoading = true;
      try {
        let apiUrl = `/student/list?limit=${this.limit}&page=${this.currentPage}`;

        if (this.searchQuery) {
          apiUrl += `&q=${this.searchQuery}`;
        }

        if (this.genderFilter) {
          apiUrl += `&gender=${this.genderFilter}`;
        }
        if (this.classFilter) {
          apiUrl += `&className=${this.classFilter}`;
        }

        const token = localStorage.getItem("token"); // Replace 'your_token_key' with your actual token key

        // Make a fetch request with the token in the headers
        const response = await this.$axios.get(apiUrl, {
          headers: {
            Authorization: `${token}`, // Attach the token in the Authorization header
            "Content-Type": "application/json", // Set the Content-Type header accordingly
            // Add any other required headers here
          },
          // Add body and other options if needed
        });
        const data = response.data;
        console.log(data);

        this.totalStudents = data.totalStudents;
        this.students = data.students;
        this.totalPages = data.totalPages;
      } catch (error) {
        console.error(error);
      } finally {
        this.isContentLoading = false;
      }
    },
    async submitPayment() {
      // Assign the selected student ID to the payment object here
      this.payment.student = this.selectedStudent?._id;

      try {
        const token = localStorage.getItem("token"); // Retrieve the token from localStorage

        const response = await axios.post(
          `/student/create-payment/${this.payment.student}`,
          this.payment,
          {
            headers: {
              "Content-Type": "application/json",
              Authorization: `${token}`, // Include the token in the Authorization header
            },
          }
        );

        if (response.status === 201) {
          // Handle successful payment submission
          console.log("Payment submitted successfully!");
        } else {
          // Handle errors if submission fails
          console.error("Payment submission failed.");
        }
      } catch (error) {
        console.error("Error submitting payment:", error);
      }
    },
    async fetchClassess() {
      try {
        const token = localStorage.getItem("token"); // Retrieve the token from localStorage

        const headers = {
          Authorization: `${token}`, // Include the token in the header
        };

        const response = await this.$axios.get("/class/list", { headers });

        this.classNames = response.data;
      } catch (error) {
        console.error("Error fetching classes:", error.response.data);
        // Handle error as needed
      }
    },
    setPage(page) {
      this.currentPage = page;
      this.fetchData();
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
        this.fetchData();
      }
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
        this.fetchData();
      }
    },
    convertToBengaliNumber(number) {
      return number.toLocaleString("bn-IN", { useGrouping: false });
    },
    showModal(student) {
      this.selectedStudent = student;
    },
    formatDate(dateString) {
      const date = new Date(dateString);
      const options = { day: "numeric", month: "short", year: "numeric" };
      return date.toLocaleDateString("en-US", options);
    },
    saveMonthlyFee() {
      // Retrieve token from localStorage
      const token = localStorage.getItem("token");

      // Define the API endpoint URL
      const apiUrl = `/student/set-monthly-fee/${this.selectedStudent._id}`;

      let monthlyFee = this.selectedStudent.monthlyFee;
      //  alert(this.selectedStudent)
      // Prepare the data to be sent in the request body
      const requestData = {
        // Include any data you need to send
        monthlyFee: parseInt(this.selectedStudent.monthlyFee),
      };
      //  alert(requestData)

      // Define the Axios configuration object
      const config = {
        headers: {
          // Include the authorization header with the token
          Authorization: `${token}`,
        },
      };

      // Make the POST request using Axios
      this.$axios
        .post(apiUrl, requestData, config)
        .then((response) => {
          // Handle success response
          alert("Monthly fee saved successfully!");
          console.log(response.data);
          window.location.reload();

          this.fetchData();
        })
        .catch((error) => {
          // Handle error response
          alert("Failed to save monthly fee. Please try again.");
          console.error(error);
        });
    },
    clickCancel() {
      this.fetchData();
      this.selectedStudent = {};
    },
  },
};
</script>