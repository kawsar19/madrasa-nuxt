<template>
	<div class="container">
		<div v-if="isLoading">
			<h2>Loading</h2>
		</div>
		<!-- Your dashboard content -->
		<div v-else class="position-relative mt-5">
			<div class="d-flex align-items-end justify-content-between">
				<h5 class="fw-bold">স্বাগতম <span id="teacherName" class="text-danger">{{ teacherName }}</span></h5>
				<img :src="userImage" height="60" width="60" class="rounded-circle  border-6" alt="">
			</div>
			<small>{{ teacherMadrasa }}</small>
			<p>{{ date }}</p>
			<div class="dashboard">
				<div class="row g-4">
					<div class="col-sm-6 col-md-4">
						<div class="info-card teachers" @click="window.location.href = '/teacher/student-list.html'">
							<h2>মোট ক্লাশ</h2>


							<div v-if="total">
								<p class="quantity">{{ total.classesCount }}</p>
							</div>
							<div v-else>
								<div class="loader-text"></div>
							</div>
						</div>
					</div>
					<div class="col-sm-6 col-md-4">
						<div class="info-card teachers" @click="window.location.href = '/teacher/student-list.html'">
							<h2>মোট শিক্ষক</h2>


							<div v-if="total">
								<p class="quantity">{{ total.teachersCount }}</p>
							</div>
							<div v-else>
								<div class="loader-text"></div>
							</div>
						</div>
					</div>
					<div class="col-sm-6 col-md-4">
						<div class="info-card students">
							<h2>মোট শিক্ষার্থী</h2>
							<div v-if="total">
								<p class="quantity">{{ total.studentsCount }}</p>
							</div>
							<div v-else>
								<div class="loader-text"></div>
							</div>

						</div>
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
			isLoading: true,
			teacherName: '',
			teacherMadrasa: '',
			totalStudents: null,
			totalClasses: null, // Fixed variable name
			totalTeachers: null,
			date: '',
			userImage: '',
			total: ''
		};
	},
	mounted() {
		this.isLoading = false;
		const token = localStorage.getItem('token');
		if (!token) {
			// window.location.href = '/login';
		} else {
			const storedUser = localStorage.getItem('user');
			if (storedUser) {
				const user = JSON.parse(storedUser);
				console.log(user)
				this.teacherName = user.name;
				this.teacherMadrasa = user.madrasaName
			}
		}
		const storedUser = localStorage.getItem('user');
		if (storedUser) {
			const user = JSON.parse(storedUser);
			this.teacherName = user.name;
			this.teacherMadrasa = user.madrasaName;
			this.userImage = user.image
			this.fetchData(user.madrasa);

		}
		// Retrieve totalStudents and totalTeachers from sessionStorage
		const storedTotalStudents = sessionStorage.getItem('totalStudents');
		const storedTotalTeachers = sessionStorage.getItem('totalTeachers');
		const storedTotalClasses = sessionStorage.getItem('totalClasses'); // Include this line

		// Check if the values exist in sessionStorage
		if (storedTotalStudents && storedTotalTeachers && storedTotalClasses) {
			// Assign the values to Vue data properties
			this.totalStudents = parseInt(storedTotalStudents);
			this.totalTeachers = parseInt(storedTotalTeachers);
			this.totalClasses = parseInt(storedTotalClasses); // Assign totalClasses

		} else {
			// If values are not in sessionStorage, fetch the data
		}
	},
	methods: {
		logout() {
			localStorage.removeItem('token');
			localStorage.removeItem('user');
			window.location.href = '../teacher-login.html';
		},
		fetchData(madrasa) {

			console.log(this.user)
			this.$axios.get(`/madrasa/totalcount/${madrasa}`, {
				headers: {
					'Authorization': `${localStorage.getItem('token')}`
				}
			})
				.then(response => {
					console.log(response);
					this.total = response.data;
				})
				.catch(error => {
					console.error('Error fetching data:', error);
				});
		}
	}
}
</script>

<style></style>