<template>
  <div class="jumbotron vertical-center">
    <div class="container">
      <div class="row">
        <div class="col-sm-12">
          <h1>Accounts</h1>
          <hr />
          <br />
          <b-alert v-if="showMessage" variant="success" show>{{ message }}</b-alert>

          <button type="button" class="btn btn-success btn-sm" v-b-modal.account-modal>
            Create Account
          </button>
          <br /><br />
          <table class="table table-hover">
            <thead>
              <tr>
                <th scope="col">Account Name</th>
                <th scope="col">Account Number</th>
                <th scope="col">Account Balance</th>
                <th scope="col">Account Currency</th>
                <th scope="col">Account Status</th>
                <th scope="col">Actions</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="account in accounts" :key="account.id">
                <td>{{ account.name }}</td>
                <td>{{ account.account_number }}</td>
                <td>{{ account.balance }}</td>
                <td>{{ account.currency }}</td>
                <td>
                  <span v-if="account.status == 'Active'" class="badge badge-success">{{ account.status }}</span>
                  <span v-else class="badge badge-danger">{{ account.status }}</span>
                </td>
                <td>
                  <div class="btn-group" role="group">
                    <button type="button" class="btn btn-info btn-sm" v-b-modal.edit-account-modal @click="editAccount(account)">
                      Edit
                    </button>
                    <button type="button" class="btn btn-danger btn-sm" @click="deleteAccount(account)">
                      Delete
                    </button>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
          <footer class="text-center">
            Copyright &copy; All Rights Reserved.
          </footer>
        </div>
      </div>

      <!-- Create Account Modal -->
      <b-modal ref="addAccountModal" id="account-modal" title="Create a new account" hide-backdrop hide-footer>
        <b-form @submit="onSubmit" class="w-100">
          <b-form-group id="form-name-group" label="Account Name:" label-for="form-name-input">
            <b-form-input id="form-name-input" type="text" v-model="createAccountForm.name" placeholder="Account Name" required></b-form-input>
          </b-form-group>
          <b-form-group id="form-currency-group" label="Currency:" label-for="form-currency-input">
            <b-form-input id="form-currency-input" type="text" v-model="createAccountForm.currency" placeholder="$ or €" required></b-form-input>
          </b-form-group>
          <!-- Country field -->
          <b-form-group id="form-country-group" label="Country:" label-for="form-country-input">
            <b-form-input id="form-country-input" type="text" v-model="createAccountForm.country" placeholder="Country" required></b-form-input>
          </b-form-group>
          <b-button type="submit" variant="outline-info">Submit</b-button>
        </b-form>
      </b-modal>

      <!-- Edit Account Modal -->
      <b-modal ref="editAccountModal" id="edit-account-modal" title="Edit the account" hide-backdrop hide-footer>
        <b-form @submit="onSubmitUpdate" class="w-100">
          <b-form-group id="form-edit-name-group" label="Account Name:" label-for="form-edit-name-input">
            <b-form-input id="form-edit-name-input" type="text" v-model="editAccountForm.name" placeholder="Account Name" required></b-form-input>
          </b-form-group>
          <!-- Country field in edit form -->
          <b-form-group id="form-edit-country-group" label="Country:" label-for="form-edit-country-input">
            <b-form-input id="form-edit-country-input" type="text" v-model="editAccountForm.country" placeholder="Country" required></b-form-input>
          </b-form-group>
          <b-button type="submit" variant="outline-info">Update</b-button>
        </b-form>
      </b-modal>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "AppAccounts",
  data() {
    return {
      accounts: [],
      createAccountForm: {
        name: "",
        currency: "",
        country: ""  // Added country field
      },
      editAccountForm: {
        id: "",
        name: "",
        country: ""  // Added country field
      },
      showMessage: false,
      message: "",
    };
  },
  methods: {
    // Fetch Accounts
    RESTgetAccounts() {
      const path = `${process.env.VUE_APP_ROOT_URL}/accounts`;
      axios
        .get(path)
        .then((response) => {
          this.accounts = response.data.accounts;
        })
        .catch((error) => {
          console.error(error);
        });
    },

    // Create Account
    RESTcreateAccount(payload) {
      const path = `${process.env.VUE_APP_ROOT_URL}/accounts`;
      axios
        .post(path, payload)
        .then(() => {
          this.RESTgetAccounts();
          this.message = "Account Created successfully!";
          this.showMessage = true;
          setTimeout(() => {
            this.showMessage = false;
          }, 3000);
        })
        .catch((error) => {
          console.error(error);
        });
    },

    // Update Account
    RESTupdateAccount(payload, accountId) {
      const path = `${process.env.VUE_APP_ROOT_URL}/accounts/${accountId}`;
      axios
        .put(path, payload)
        .then(() => {
          this.RESTgetAccounts();
          this.message = "Account Updated successfully!";
          this.showMessage = true;
          setTimeout(() => {
            this.showMessage = false;
          }, 3000);
        })
        .catch((error) => {
          console.error(error);
        });
    },

    // Delete Account
    RESTdeleteAccount(accountId) {
      const path = `${process.env.VUE_APP_ROOT_URL}/accounts/${accountId}`;
      axios
        .delete(path)
        .then(() => {
          this.RESTgetAccounts();
          this.message = "Account Deleted successfully!";
          this.showMessage = true;
          setTimeout(() => {
            this.showMessage = false;
          }, 3000);
        })
        .catch((error) => {
          console.error(error);
        });
    },

    // Form Submission Handlers
    onSubmit(e) {
      e.preventDefault();
      this.$refs.addAccountModal.hide();
      const payload = {
        name: this.createAccountForm.name,
        currency: this.createAccountForm.currency,
        country: this.createAccountForm.country  // Send country in payload
      };
      this.RESTcreateAccount(payload);
      this.initForm();
    },

    onSubmitUpdate(e) {
      e.preventDefault();
      this.$refs.editAccountModal.hide();
      const payload = {
        name: this.editAccountForm.name,
        country: this.editAccountForm.country  // Send country in payload
      };
      this.RESTupdateAccount(payload, this.editAccountForm.id);
      this.initForm();
    },

    // Edit and Delete Handlers
    editAccount(account) {
      this.editAccountForm = { ...account };
    },

    deleteAccount(account) {
      this.RESTdeleteAccount(account.id);
    },

    // Initialize form
    initForm() {
      this.createAccountForm.name = "";
      this.createAccountForm.currency = "";
      this.createAccountForm.country = "";  // Reset country field
      this.editAccountForm.id = "";
      this.editAccountForm.name = "";
      this.editAccountForm.country = "";  // Reset country field
    }
  },

  created() {
    this.RESTgetAccounts();
  },
};
</script>
