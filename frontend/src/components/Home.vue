<template>
    <div class="jumbotron mt-4">
        <h1 class="display-4">Hello! Everyone <br> Welcome to our Employee Management Portal</h1>
        <p class="lead">
            This is a simple employee application, made by Vuejs CDN and Django Rest-API Framework && Poetry Package Management.
            This allows you to (GET, CREATE, EDIT, DELETE) your details and send emails to employees.
        </p>
        
        <!-- Email Section -->
        <div class="email-section mt-4 p-4 border rounded">
            <h3>📧 Send Email to Employees</h3>
            
            <div v-if="loading" class="alert alert-info">
                Sending email...
            </div>
            
            <div v-if="successMessage" class="alert alert-success">
                {{ successMessage }}
            </div>
            
            <div v-if="errorMessage" class="alert alert-danger">
                {{ errorMessage }}
            </div>
            
            <form @submit.prevent="sendEmail">
                <div class="form-group">
                    <label for="emailSubject">Subject</label>
                    <input 
                        type="text" 
                        class="form-control" 
                        id="emailSubject" 
                        v-model="emailData.subject"
                        placeholder="Enter email subject"
                        required
                    >
                </div>
                
                <div class="form-group">
                    <label for="emailMessage">Message</label>
                    <textarea 
                        class="form-control" 
                        id="emailMessage" 
                        v-model="emailData.message"
                        rows="4"
                        placeholder="Enter your message"
                        required
                    ></textarea>
                </div>
                
                <div class="form-group">
                    <label for="emailRecipients">Recipients</label>
                    <select 
                        class="form-control" 
                        id="emailRecipients" 
                        v-model="emailData.recipients"
                        multiple
                    >
                        <option value="all">All Employees</option>
                        <option value="hr@company.com">HR Department</option>
                        <option value="admin@company.com">Administration</option>
                        <!-- Dynamic employee emails can be loaded here -->
                    </select>
                    <small class="form-text text-muted">Hold Ctrl/Cmd to select multiple recipients</small>
                </div>
                
                <button type="submit" class="btn btn-primary" :disabled="loading">
                    {{ loading ? 'Sending...' : 'Send Email' }}
                </button>
            </form>
        </div>
        
        <hr class="my-4">
        
        <!-- Quick Actions -->
        <div class="quick-actions">
            <a href="/employees" class="btn btn-primary btn-lg mr-2">View Employees</a>
            <a href="/add-employee" class="btn btn-success btn-lg mr-2">Add New Employee</a>
            <button @click="showEmailModal = true" class="btn btn-info btn-lg">Quick Email</button>
        </div>
    </div>
</template>

<script>
export default {
    name: "Home",
    data() {
        return {
            emailData: {
                subject: '',
                message: '',
                recipients: []
            },
            loading: false,
            successMessage: '',
            errorMessage: '',
            showEmailModal: false
        };
    },
    methods: {
        async sendEmail() {
            this.loading = true;
            this.successMessage = '';
            this.errorMessage = '';
            
            try {
                const response = await fetch('http://localhost:8000/api/send-email/', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify(this.emailData)
                });
                
                const data = await response.json();
                
                if (response.ok) {
                    this.successMessage = data.status || 'Email sent successfully!';
                    // Reset form
                    this.emailData = {
                        subject: '',
                        message: '',
                        recipients: []
                    };
                } else {
                    this.errorMessage = data.error || 'Failed to send email';
                }
            } catch (error) {
                this.errorMessage = 'Network error: ' + error.message;
            } finally {
                this.loading = false;
            }
        }
    }
}
</script>

<style scoped>
.email-section {
    background-color: #f8f9fa;
    max-width: 600px;
    margin: 0 auto;
}

.quick-actions {
    margin-top: 2rem;
    text-align: center;
}

.form-control:focus {
    border-color: #007bff;
    box-shadow: 0 0 0 0.2rem rgba(0, 123, 255, 0.25);
}
</style>
