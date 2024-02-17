<template>
    <form @submit.prevent="login" class="border p-4">
        <h3 class="h3 text-primary">Teacher Login </h3>
        <div class="form-group">
            <label for="phone">Phone:</label>
            <input type="text" class="form-control shadow-none" id="phone" v-model="phone" required>
        </div>
        <div class="form-group">
            <label for="password">Password:</label>
            <input type="password" class="form-control shadow-none" id="password" v-model="password" required>
        </div>
        <button type="submit" class="btn btn-primary w-100 d-block" :disabled="isLoading">
            <span v-if="isLoading" class="spinner-border spinner-border-sm" role="status" aria-hidden="true"></span>
            <span>{{ isLoading ? 'Loading' : 'Login' }}</span>
        </button>
    </form>
</template>

<script>
export default {
    data() {
        return {
            phone: '0466577865',
            password: '111111',
            isLoading: false,
        };
    },

    methods: {
        async login() {
            this.isLoading = true;
            console.log(this.isLoading)
            try {
                const response = await this.$axios.post('/teacher/login', {
                    phone: this.phone,
                    password: this.password
                });
                console.log(response)
                // this.$toasted.success('Login successfull')

                const { token, user } = response.data;

                // Store token and user in localStorage
                localStorage.setItem('token', token);
                localStorage.setItem('user', JSON.stringify(user));

                // Redirect to dashboard after successful login

                // Redirect to dashboard after successful login
                setTimeout(() => {
                    this.$router.push('/');
                }, 1500);
            } catch (error) {
                console.error(error.response.data.error);
                // Handle login errors
                this.$toasted.error('Login failed')

            } finally {
                this.isLoading = false;
            }
        },

    }
};
</script>
