<template>
    <div class="container py-5">
        <h3>স্টুডেন্ট রেজিস্টার </h3>

        <form @submit.prevent="registerStudent">
            <!-- Form fields for student registration -->
            <div class="row g-3">

                <div class="col-12 col-md-6">
                    <div class="input-group">
                        <label for="fullName" class="input-group-text">নাম:</label>
                        <input type="text" class="form-control shadow-none" id="fullName" v-model="student.fullName"
                            required>
                    </div>
                </div>
                <div class="col-12 col-md-6">
                    <div class="input-group">
                        <label for="dob" class="input-group-text">জন্ম:</label>
                        <input type="date" class="form-control shadow-none" id="dob" v-model="student.dateOfBirth" required>
                    </div>
                </div>

                <div class="col-12 col-md-6">
                    <div class="input-group">
                        <label for="gender" class="input-group-text">লিঙ্গ:</label>
                        <select class="form-select shadow-none" id="gender" v-model="student.gender" required>
                            <option value="" disabled selected>Select gender</option>
                            <option value="Male">Male</option>
                            <option value="Female">Female</option>
                            <option value="Other">Other</option>
                        </select>
                    </div>
                </div>

                <div class="col-8 col-md-6">
                    <div class="input-group">
                        <label for="studentImage" class="input-group-text">ছবি:</label>
                        <input class="form-control shadow-none input-sm" id="studentImage" type="file"
                            @change="onFileChange" accept="image/*">
                    </div>
                </div>
                <div class="col-4 col-md-6">
                    <div v-if="!uploadedImage && !isloading">
                        <img src="https://placehold.co/400" alt="" class="image-thumnail">
                    </div>

                    <div v-if="isloading">
                        <div class="spinner-border text-primary" role="status"></div>
                        <span class="sr-only"></span>
                    </div>

                    <img v-else :src="uploadedImage" alt="" class="image-thumnail">
                </div>

                <div class="col-12 col-md-6">
                    <div class="input-group">
                        <label for="dob" class="input-group-text">ফোন:</label>
                        <input type="text" class="form-control shadow-none" id="phone" v-model="student.phone" required>
                    </div>
                </div>
                <div class="col-12 col-md-6">
                    <div class="input-group">
                        <label for="dob" class="input-group-text">গার্ডিয়ান :</label>
                        <input type="text" class="form-control shadow-none" id="phone" v-model="student.guardian.fullName"
                            required>
                    </div>
                </div>

                <div class="col-12 col-md-6">
                    <div class="input-group">
                        <label for="studentClass" class="input-group-text">শ্রেণী</label>
                        <select class="form-select shadow-none" id="studentClass" v-model="student.studentClass" required>
                            <option value="" disabled selected>শ্রেণী নির্বাচন করুন</option>
                            <option v-for="classItem in classess" :key="classItem._id" :value="classItem._id">
                                {{ classItem.className }}
                            </option>
                        </select>
                    </div>

                </div>

                <div class="col-12 col-md-6">
                    <div class="input-group">
                        <label for="village" class="input-group-text">গ্রাম/ পাড়া:</label>
                        <input type="text" v-model="student.address.village" class="form-control shadow-none" required>
                    </div>
                </div>

                <div class="col-6 col-md-3">
                    <div class="input-group">
                        <label for="divisionSelect" class="input-group-text">বিভাগ</label>
                        <select id="divisionSelect" v-model="selectedDivision" @change="handleDivisionChange"
                            class="form-select shadow-none">
                            <option value="" disabled>বিভাগ নির্বাচন করুন</option>
                            <option v-for="division in divisions" :value="division" class="h1">{{ division.bn_name }}
                            </option>
                        </select>
                    </div>
                </div>


                <div class="col-6 col-md-3">
                    <div class="input-group">
                        <label for="districtSelect" class="input-group-text">জেলা</label>
                        <select id="districtSelect" v-model="selectedDistrict" @change="handleDistrictChange"
                            class="form-select shadow-none" :disabled="!selectedDivision">
                            <option value="" disabled>Select District</option>
                            <option v-for="district in filteredDistricts" :value="district">{{ district.bn_name }}</option>
                        </select>
                    </div>
                </div>
                <div class="col-6 col-md-3">
                    <div class="input-group">
                        <label for="upazilaSelect" class="input-group-text">উপজেলা</label>
                        <select id="upazilaSelect" v-model="selectedUpazila" class="form-select shadow-none"
                            :disabled="!selectedDistrict">
                            <option value="" disabled>Select Upazila</option>
                            <option v-for="upazila in filteredUpazilas" :value="upazila">{{ upazila.bn_name }}</option>
                        </select>
                    </div>
                </div>
                <div class="col-6 col-md-3">
                    <div class="input-group">
                        <label for="unionSelect" class="input-group-text">ইউনিয়ন</label>
                        <select id="unionSelect" v-model="selectedUnion" class="form-select shadow-none"
                            :disabled="!selectedUpazila">
                            <option value="" disabled>Select Union</option>
                            <option v-for="union in filteredUnions" :value="union">{{ union.bn_name }}</option>
                        </select>
                    </div>
                </div>

            </div>

            <!-- Inside the modal form -->

            <button type="submit" class="btn btn-success mt-5 w-100 ">রেজিস্ট্রার </button>
            <div class="gap"></div>
            <div class="gap"></div>
            <div class="gap"></div>

        </form>

    </div>
</template> 
<script>
export default {
    data() {
        return {
            divisions: [],
            districts: [],
            upazilas: [],
            unions: [],
            selectedDivision: null,
            selectedDistrict: null,
            selectedUpazila: null,
            selectedUnion: null,
            students: [],
            selectedStudent: null,
            isloading: false,
            selectedFile: null,
            uploadedImage: null,
            student: {
                fullName: 'test',
                dateOfBirth: '2007-12-12',
                gender: 'Male',
                phone: '0176544577',

                studentImage: 'ok',
                address: {
                    village: 'testvil',
                    thana: '',
                    district: '',
                    division: '',
                    union: ''
                },
                guardian: {
                    fullName: 'test girdian',
                    phoneNumber: ''
                },
                studentClass: '' // Add studentClass to the data object

            },
            classess: null
        };

    },
    mounted() {
        this.fetchClassess()
        const token = localStorage.getItem('token');
        if (!token) {
            window.location.href = '../teacher-login.html'; // Replace 'login.html' with your actual login page
        } else {
            // Token exists, proceed with retrieving the teacher's name
            const storedUser = localStorage.getItem('user');
            if (storedUser) {
                const user = JSON.parse(storedUser);
                this.teacherName = user.name; // Assuming 'name' is the property containing the teacher's name
            }
        }

        // Retrieve teacher's name from local storage on page load
        const storedUser = localStorage.getItem('user');
        if (storedUser) {
            const user = JSON.parse(storedUser);
            this.teacherName = user.name; // Assuming 'name' is the property containing the teacher's name
        }
    },
    computed: {
        filteredDistricts() {
            if (!this.selectedDivision) {
                return [];
            }
            return this.districts.filter(district => district.division_id === this.selectedDivision.id);
        },
        filteredUpazilas() {
            if (!this.selectedDistrict) {
                return [];
            }
            return this.upazilas.filter(upazila => upazila.district_id === this.selectedDistrict.id);
        },
        filteredUnions() {
            if (!this.selectedUpazila) {
                return [];
            }
            return this.unions.filter(union => union.upazilla_id === this.selectedUpazila.id);
        },
    },
    methods: {
        async onFileChange(e) {
            this.isloading = true
            this.selectedFile = e.target.files[0]; // Store the selected file in selectedFile property

            try {
                const formData = new FormData();
                formData.append('image', this.selectedFile);

                const response = await fetch('https://api.imgbb.com/1/upload?key=6d9e72da0846d4357c13a69737f5ca97', {
                    method: 'POST',
                    body: formData,
                });

                const data = await response.json();
                console.log('ImageBB Upload Response:', data.data);

                // Assuming you want to store the uploaded image URL somewhere, you can access it from 'data' and save it to a variable or use it directly
                this.student.studentImage = data.data.url;
                // You can use this 'imageUrl' for further operations or store it in your database
                this.uploadedImage = data.data.url
                this.isloading = false
            } catch (error) {
                console.error('Error uploading to ImageBB:', error);
                this.isloading = false
                // Handle error as needed
            }
        },

        selectDelete(student) {
            this.selectedStudent = student
        },
        fetchData() {
            // Fetch data from JSON files
            fetch('./data/districts.json')
                .then(response => response.json())
                .then(data => {
                    this.divisions = data;
                    // Set isLoading to false after data is loaded
                });

            fetch('./data/districts.json')
                .then(response => response.json())
                .then(data => this.districts = data);

            fetch('./data/upazilas.json')
                .then(response => response.json())
                .then(data => this.upazilas = data);

            fetch('./data/unions.json')
                .then(response => response.json())
                .then(data => this.unions = data);
        },
        async getAllStudents() {
            try {
                const response = await this.$axios.get('/student/list');
                console.log(response)
                this.students = response.data;
            } catch (error) {
                console.error(error);
                // Handle error, show error message, etc.
            }
        },

        async fetchClassess() {

            try {
                const token = localStorage.getItem('token'); // Retrieve the token from localStorage
                const headers = {
                    'Authorization': `${token}` // Include the token in the header
                };
                console.log(token)

                const response = await this.$axios.get('/class/list', { headers });
                console.log(response);
                this.classess = response.data;
            } catch (error) {
                console.error('Error fetching classes:', error.response.data);
                // Handle error as needed
            }
        },

        async registerStudent() {

            try {
                const {
                    fullName,
                    dateOfBirth,
                    gender,
                    phone,
                    studentClass,
                    studentImage,

                    address: { village, thana, district, division, union },
                    guardian: { fullName: guardianFullName, phoneNumber: guardianPhoneNumber }
                } = this.student;
                console.log(this.student);

                const studentData = {
                    fullName,
                    dateOfBirth,
                    gender,
                    phone,
                    studentClass,
                    studentImage,
                    address: {
                        village,
                        upazila: this.selectedUpazila ? this.selectedUpazila.bn_name : 'Not selected',
                        district: this.selectedDistrict ? this.selectedDistrict.bn_name : 'Not selected',
                        division: this.selectedDivision ? this.selectedDivision.bn_name : 'Not selected',
                        union: this.selectedUnion ? this.selectedUnion.bn_name : 'Not selected'
                    },
                    guardian: {
                        fullName: guardianFullName,
                        phoneNumber: guardianPhoneNumber
                    },

                };
                const token = localStorage.getItem('token'); // Retrieve the token from localStorage
                const headers = {
                    'Authorization': `${token}` // Include the token in the header
                };
                console.log(studentData)

                // Send studentData to your API endpoint using Axios or fetch
                // Example using Axios:
                const response = await this.$axios.post('/student/create', studentData, { headers });

                console.log('Student data:', response);

                // Clear the form fields after successful submission
                this.student = {
                    fullName: '',
                    dateOfBirth: '',
                    gender: '',
                    phone: '',
                    studentImage,
                    address: {
                        village: '',
                        thana: '',
                        district: '',
                        division: '',
                        union: ''
                    },
                    guardian: {
                        fullName: '',
                        phoneNumber: ''
                    },
                    studentClass: ''

                };
                window.location.reload()

                // Hide the modal after successful submission
                //const modal = new bootstrap.Modal(document.getElementById('studentModal'));
                //  modal.hide();
            } catch (error) {
                console.log(error)
                console.error(error.response.data);
                alert(error.response.data.error)
                // Handle errors accordingly
            }
        },
        async confirmDelete(id) {
            alert(id)
            try {
                const response = await this.$axios.delete('/student/delete/' + id);
                fetchTeachers()
            } catch (error) {
                console.error('Error deleting teacher  ', error)

            }
        },

        handleDivisionChange() {
            this.selectedDistrict = null;
            this.selectedUpazila = null;
            this.selectedUnion = null;
        },
        handleDistrictChange() {
            this.selectedUpazila = null;
            this.selectedUnion = null;
        },
    },

    created() {
        this.fetchData();
        //  this.getAllStudents()
    }

}
</script>
