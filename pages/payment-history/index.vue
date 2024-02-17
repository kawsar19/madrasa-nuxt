<template>
    <div class="container">
        <h1>Payment Search</h1>
        <div class="form-group d-flex gap-2 align-items-start ">
            <input type="text" class="form-control mb-2 shadow-none " v-model="phoneNumber"
                placeholder="Enter phone number">
            <button class="btn btn-success shadow-none px-4  " @click="searchPayments">Search</button>
        </div>


        <div v-if="payments.length > 0" class="mt-4">
            <h2>Payments</h2>
            <ul class="list-group mb-4">
                <li class="list-group-item">
                    <b>নামঃ</b> <span>{{ student.fullName }}</span>
                </li>
                <li class="list-group-item">
                    <b>শ্রেণী</b> <span>{{ student.studentClass.className }}</span>

                </li>
            </ul>
            <table class="table table-striped">
                <thead>
                    <tr>

                        <th>মাস</th>
                        <th>বছর</th>
                        <th>টাকার পরিমাণ</th>
                        <th>পরিশোধ</th>
                        <th>জমা নিয়েছেন</th>

                    </tr>
                </thead>
                <tbody>
                    <tr v-for="payment in payments" :key="payment._id">
                        <td>{{ payment.month }}</td>
                        <td>{{ payment.year }}</td>
                        <td>{{ payment.amount }}</td>
                        <td>
                            <span :class="{ 'badge bg-success': payment.paid, 'badge bg-danger': !payment.paid }">
                                {{ payment.paid ? 'পরিশোধ' : 'বাকি' }}
                            </span>
                        </td>

                        <td>{{ payment.receivedBy?.name }}</td>
                    </tr>
                </tbody>
            </table>
        </div>

        <div v-else-if="searched && !loading" class="alert alert-warning mt-4">
            No payments found for the provided phone number and year.
        </div>

        <div v-if="loading" class="mt-4">
            Loading...
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            phoneNumber: '',
            payments: [],
            student: {},
            searched: false,
            loading: false
        }
    },
    methods: {
        async searchPayments() {
            this.loading = true;
            this.searched = true;
            try {
                const response = await this.$axios.get(`/payment/single-payment?phoneNumber=${this.phoneNumber}`);
                this.payments = response.data.payments;
                this.student = response.data.student
            } catch (error) {
                console.error('Error fetching payments:', error);
            } finally {
                this.loading = false;
            }
        }
    }
}
</script>

<style scoped>
th {
    color: #fff;
}
</style>