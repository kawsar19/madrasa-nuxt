<template>
    <div class="container py-5">
        <div class="table-responsive">
            <table class="table table-striped">
                <thead>
                    <tr>
                        <th>Name</th>
                        <th>Phone</th>
                        <th>Village</th>
                        <th>Upazila</th>
                        <th>District</th>
                        <th>Division</th>
                        <th>Union</th>
                        <th>Emergency Phone</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="teacher in teachers" :key="teacher._id">
                        <td>{{ teacher.name }}</td>
                        <td>{{ teacher.phone }}</td>
                        <td>{{ teacher.village }}</td>
                        <td>{{ teacher.upazila }}</td>
                        <td>{{ teacher.district }}</td>
                        <td>{{ teacher.division }}</td>
                        <td>{{ teacher.union }}</td>
                        <td>{{ teacher.emergencyPhoneNumber }}</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    data() {
        return {
            teachers: []
        };
    },
    mounted() {
        this.fetchTeachers();
    },
    methods: {
        async fetchTeachers() {
            try {
                // Get the token from localStorage
                const token = localStorage.getItem('token');
                const user = JSON.parse(localStorage.getItem('user'))

                // If token is not available, handle the error or return
                if (!token || !user) {
                    console.error('Token or user not found in localStorage');
                    return;
                }

                const response = await this.$axios.get('/teacher/madrasa-teachers', {
                    params: {
                        // Add your parameters here
                        // For example:
                        madrasa: user.madrasa,
                        // param2: value2,
                    },
                    headers: {
                        // Pass the token in the Authorization header
                        'Authorization': `${token}`
                    }
                });
                this.teachers = response.data.teachers;
                console.log(response.data.teachers);
            } catch (error) {
                console.error('Error fetching teachers:', error);
            }
        }

    }
};
</script>

<style>
th {
    color: #fff;
}
</style>
