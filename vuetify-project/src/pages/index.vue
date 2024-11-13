
<template>

  <!--App bar that displays title-->
    <v-app-bar color="primary" :elevation="2">

  <!--Title-->
  <v-app-bar-title class="text-center">
  <v-app-bar-nav-icon></v-app-bar-nav-icon>
  FRAMEWORKS
  </v-app-bar-title>

  <!--Brings up the form-->
  <template v-slot:append>
  <v-btn 
  class="right" 
  :elevation="2" 
  :prepend-icon 
  @click="openAddForm"
  color="white">
  <v-icon icon="mdi-plus-circle" />
  ADD
    </v-btn>
  </template>
  </v-app-bar>

  <!--Overlay with inputs that displays after add is pressed-->
  <v-overlay 
  v-model="add" 
  class="align-center justify-center" 
  scroll-strategy="block">

    <v-fade-transition hide-on-leave>

      <!--Form for adding tasks-->
    <v-card
      min-height="550"
      v-if="add"
      elevation="16"
      min-width="350"
      max-width="350"
      style="position: relative;">

      <!--Title for Overlay-->
      <v-toolbar color="primary">
      <v-toolbar-title><v-icon icon="mdi-plus-circle"/> Add Task</v-toolbar-title>
      <v-spacer></v-spacer>
    </v-toolbar>

    <!--Prevents form from being submitted if these arent filled out-->
    <v-form 
    ref="taskForm" 
    v-model="formValid" 
    @submit.prevent 
    class="d-flex flex-column align-center" 
    style="min-width: 220px; padding: 30px;">
    
      <!--Title entry-->
      <v-text-field
      max-width="300"
      min-width="300"
      style="margin: 10px;"
      v-model="title"
      :rules="[titleRules, titleUnique]"
      label="Title"
      variant="outlined"
    ></v-text-field>

    <!--Description entry-->
    <v-text-field
      max-width="300"
      min-width="300"
      style="margin: 10px;"
      v-model="description"
      :rules="descRules"
      label="Description"
      variant="outlined">
    </v-text-field>

    <!--Date entry-->
    <v-text-field 
      @focus="showDatePicker = true"
      :value="formattedDate"
      max-width="300"
      min-width="300"
      style="margin: 10px;"
      v-model="date1"
      clearable label="Deadline" 
      variant="outlined">
    </v-text-field>

    <!--Priority picker-->
    <v-radio-group 
    inline label="Priority" 
    style="padding: 0px;"
    v-model="priorityButton">

    <!--Low-->
    <v-radio 
    label="Low" 
    value="Low"
    style="margin-right: 10px; margin-top: 0px;">
    </v-radio>

  <!--Medium-->
    <v-radio 
    label="Med" 
    value="Med"
    style="margin-right: 10px; margin-left: 10px;">
    </v-radio>

  <!--High-->
    <v-radio 
    label="High" 
    value="High"
    style="margin-right: 25px; margin-left: 10px;">
    </v-radio>
  </v-radio-group>

  <!--Adds task and closes overlay-->
  <v-btn 
  type="submit"
  style=" 
    position: absolute; 
    bottom: 16px; 
    left: 95px;
    padding-left: 30px; 
    padding-right: 30px;"
  :elevation="2" 
  :prepend-icon
  @click="validateAndAddRow"
  color="primary">
  <v-icon icon="mdi-plus-circle"/>
  ADD
    </v-btn>

    <!--Closes the overlay-->
    <v-btn 
  style="position: absolute; bottom: 16px; right: 16px; padding-left: 20px; padding-right: 20px;"
  :elevation="2" 
  :prepend-icon 
  @click="add = !add"
  color="red">
  <v-icon icon="mdi-cancel"></v-icon>
  CANCEL
    </v-btn>
  </v-form>
  </v-card>
  </v-fade-transition>
  </v-overlay>

<!-- Date picker component that appears based on the showDatePicker flag -->
  <v-dialog
    v-model="showDatePicker"
    width="290">
  <v-container>
    <v-date-picker 
      elevation="24"
      v-model="date1"
      @update:model-value="onDateSelected">
    </v-date-picker>
  </v-container>
  </v-dialog>

  <!--Displays when form submission is successful-->
  <v-snackbar 
  v-model="formSuccess" 
  :timeout="timeout"
  color="success">

  Task added successfully
  <template v-slot:actions>
    <v-btn 
    color="white" 
    variant="text" 
    @click="formSuccess = false">
      Close
    </v-btn>
  </template>
</v-snackbar>


<!--Displays when fields need to be filled out-->
<v-snackbar 
v-model="formFailure" 
:timeout="timeout"
color="error">

All forms need to be completed
<template v-slot:actions>
  <v-btn
  color="white" 
  variant="text" 
  @click="formFailure = false">
    Close
  </v-btn>
</template>
</v-snackbar>

<!--Displays when the title is not unique-->
<v-snackbar 
v-model="formTitleFailure" 
:timeout="timeout"
color="error">

Title must be unique
<template v-slot:actions>
  <v-btn
  color="white" 
  variant="text" 
  @click="formTitleFailure = false">
    Close
  </v-btn>
</template>
</v-snackbar>


  <!--Overlay with inputs that displays after update is pressed-->
  <v-overlay 
  v-model="update" 
  class="align-center justify-center" 
  scroll-strategy="block">

    <v-fade-transition hide-on-leave>

      <!--Form for adding tasks-->
    <v-card
      min-height="450"
      v-if="update"
      elevation="16"
      min-width="350"
      max-width="350"
      style="position: relative;">

      <!--Title for Overlay-->
      <v-toolbar color="primary">
      <v-toolbar-title>
        <v-icon icon="mdi-application-edit"></v-icon> 
        Edit Task
      </v-toolbar-title>
      <v-spacer></v-spacer>
    </v-toolbar>

    <!--Update form-->
    <v-form 
    ref="taskForm" 
    v-model="formValid" 
    class="d-flex flex-column align-center" 
    style="min-width: 220px; padding: 30px;">

    <!--Updated description entry-->
    <v-text-field
      max-width="300"
      min-width="300"
      style="margin: 10px;"
      v-model="description"
      label="Description"
      variant="outlined"
      :rules="descRules">
    </v-text-field>

    <!--Updated date entry-->
    <v-text-field 
      @focus="showUpdatedDatePicker = true"
      :value="updatedFormattedDate"
      max-width="300"
      min-width="300"
      style="margin: 10px;"
      v-model="date2"
      clearable label="Deadline" 
      variant="outlined">
    </v-text-field>

    <!--Updated priority picker-->
    <v-radio-group 
    inline label="Priority" 
    style="padding: 0px;"
    v-model="priorityButton">

    <!--Low-->
    <v-radio 
    label="Low" 
    value="Low"
    style="margin-right: 10px; margin-top: 0px;">
    </v-radio>

  <!--Medium-->
    <v-radio 
    label="Med" 
    value="Med"
    style="margin-right: 10px; margin-left: 10px;">
    </v-radio>

  <!--High-->
    <v-radio 
    label="High" 
    value="High"
    style="margin-right: 25px; margin-left: 10px;">
    </v-radio>
  </v-radio-group>

  <!--updates task and closes overlay-->
  <v-btn 
    type="submit"
    style=" 
      position: absolute; 
      bottom: 16px; 
      left: 95px;
      padding-left: 30px; 
      padding-right: 30px;"
    :elevation="2" 
    :prepend-icon
    @click="validateAndUpdateRow"
    color="primary">
    <v-icon icon="mdi-application-edit"/>
    EDIT
  </v-btn>

    <!--Closes the overlay-->
    <v-btn 
      style="position: absolute; bottom: 16px; right: 16px; padding-left: 20px; padding-right: 20px;"
      :elevation="2" 
      :prepend-icon 
      @click="update = false"
      color="red">
      <v-icon icon="mdi-cancel"></v-icon>
      CANCEL
    </v-btn>
  </v-form>
</v-card>
</v-fade-transition>
  </v-overlay>

<!--Datepicker to update the date-->
  <v-dialog
    v-model="showUpdatedDatePicker"
    width="290">
  <v-container>
    <v-date-picker 
      elevation="24"
      v-model="date2"
      @update:model-value="onUpdatedDateSelected">
    </v-date-picker>
  </v-container>
  </v-dialog>

  <!--Displays when edit is clicked and successful-->
  <v-snackbar 
  v-model="updateSuccess" 
  :timeout="3000"
  color="success">
  Task updated successfully
  <template v-slot:actions>
    <v-btn 
    color="white" 
    variant="text" 
    @click="updateSuccess = false">
    Close
    </v-btn>
  </template>
</v-snackbar>

<!--Displays when delete is clicked-->
<v-snackbar 
v-model="deleteSuccess" 
:timeout="timeout"
color="success">
Task deleted successfully
<template v-slot:actions>
  <v-btn 
  color="white" 
  variant="text" 
  @click="deleteSuccess = false">
  Close
  </v-btn>
</template>
</v-snackbar>


  <!--Table for the attributes of tasks-->
  <v-card elevation="2">
  <v-container fluid>
  <v-table>
  <thead>
  <tr id="tableText">

    <!--Title-->
    <th class="text-grey-darken-1">
      Title
    </th>
    <!--Description-->
    <th class="text-grey-darken-1">
      Description
    </th>
    <!--Deadline-->
    <th class="text-grey-darken-1">
      Deadline
    </th>
    <!--Priority-->
    <th class="text-grey-darken-1">
      Priority
    </th>
    <!--Is Complete-->
    <th class="text-grey-darken-1">
      Is Complete
    </th>
    <!--Action-->
    <th class="text-grey-darken-1">
      Action
    </th>
  </tr>
  </thead>

  <!--User-created body of the table-->
  <tbody>
  <tr 
  v-for="(task, index) in valueOfTasks" 
  :key="index"
  style="height: 100px;">
    <td>{{ task.title }}</td>
    <td>{{ task.description }}</td>
    <td>{{ task.deadline }}</td>
    <td>{{ task.priority }}</td>
    <div style="margin-top: 30px; margin-left: 20px;">
    <td>
      <v-checkbox 
      @click=""
      v-model="task.isChecked">
    </v-checkbox>
    </td>
    </div>

    <!--Update and delete buttons-->
    <td>

      <!--Opens edit task overlay-->
      <v-row style="margin-top: 0px; max-width: 100px;">
        <v-btn
        v-if="!task.isChecked"
        @click="openEditForm(task)" 
        color="primary">
          <v-icon>mdi-application-edit</v-icon> UPDATE
        </v-btn>
      </v-row>

      <!--Removes selected row-->
      <v-row>
        <v-btn 
          @click="deleteRow(index)" 
          color="red"
          style="max-width: 100px;">
          <v-icon>mdi-cancel</v-icon> DELETE
        </v-btn>
      </v-row> 

    </td>
  </tr>
  </tbody>
  </v-table>
  </v-container>
  </v-card>
</template>

  <!--Runs many functions-->
  <script>
  export default {
    data() { 
      return {
        add: false, // Controls add overlay visibility
        update: false, // Controls edit overlay visibility
        valueOfTasks: [], // Controls task visibility

        showDatePicker: false, // Controls initial datepicker menu visibility
        showUpdatedDatePicker: false, // Controls updated date-picker menu visibility
        date1: null, // Holds the intial selected date
        date2: null, // Holds the updated selected date
        formattedDate: '', // Controls the properly formatted date
        updatedFormattedDate: '', // Controls the properly formatted date

        priorityButton: 'Med', // Initially selects the med radio button 
        chosenPriorityButton: "",

        editingIndex: null, // Index of the task being edited
        
        // Controls their respective table attributes
        title: '',
        description: '',
        deadline: '',
        priority: '', 

        formValid: false, // Checks whether the form is valid
        formSuccess: false, // Controls success snackbar visibility
        formFailure: false, // Controls no entry failure snackbar visibility
        formTitleFailure: false, // Controls duplicate title snackbar visibility
        deleteSuccess: false, // Controls deletion snackbar visbility
        updateSuccess: false, // Controls update snackbar visibility
        timeout: 3000, // Snackbar timeout

        // No submission if there is no title
        titleRules: [ 
        value => {
          if (value) 
          return true

          return 'First name is required.'
          this.formFailure = true
          
        },
        ],
        // No submission if there is no description
        descRules: [
        value => {
          if (value) 
          return true

          return 'Description is required.'
          this.formFailure = true
        },
        ],
      };
    },

    computed: {
      // Assures the initial date is formatted correctly
      formattedDate() {
        return this.date1 ? new Date(this.date1).toLocaleDateString("en-US") : '';
      },

      // Assures the updated date is formatted correctly
      updatedFormattedDate() {
        return this.date2 ? new Date(this.date2).toLocaleDateString("en-US") : '';
      },

      // Checks if the title is unique and displays an error if it's not
      titleUnique() {
        return value => {
          // Checks if the title already exists in the valueOfTasks array
          const exists = this.valueOfTasks.some(task => task.title === value);
          if (exists) {
            return 'Title must be unique';
          }
          return true;
        };
      },
    },
    methods: {
      // Removes the selected row
      deleteRow(index) {
        this.valueOfTasks.splice(index, 1);
        this.deleteSuccess = true;
      },

      // Opens the edit form and sets the values for the selected row
      openEditForm(task) {
        this.editingIndex = this.valueOfTasks.indexOf(task); // Gets the index of the selected task
        this.update = true;
        this.description = task.description;
        this.priorityButton = task.priority;
        this.date2 = task.deadline ? new Date(task.deadline) : null;

        // Delay setting the date to avoid conflict with the date picker
        setTimeout(() => {
          this.date1 = null; // Ensure date1 is reset for the add form
        }, 2);
      },

      // Opens the edit form and sets the values for the selected row
      openAddForm() {
        this.add = true;
        this.title = "";
        this.description = "";
        this.priorityButton = "Med";
        this.date1 = null;
        this.updateSuccess = false;
      },



      // Checks if the form is valid
      validateAndAddRow() {
  
        // Checks if the title is unique
        const titleExists = this.valueOfTasks.some(task => task.title === this.title);
        if (titleExists) {

        // Shows title failure snackbar if title isn't unique
        this.formTitleFailure = true;
        return; // Stops further processing
        }

        // Proceeds if form is valid
        if (this.formValid) {

          // Makes sure the deadline appears
          const formattedDeadline = this.date1 ? this.date1.toLocaleDateString('en-US') : '';
          this.deadline = formattedDeadline;
          

          // Puts all of the form submissions under their respective table attributes
          this.$refs.taskForm.validate();
          this.valueOfTasks.push({
            title: this.title,
            description: this.description,
            deadline: this.deadline,
            priority: this.priorityButton,
          });

          // Displays the success snackbar
          this.formSuccess = true;
  
          // Clears the form and closes the overlay
          this.resetForm();

        }
        else {
        // Form is invalid, show error snackbar
        this.formFailure = true;
        }
      },

      // Checks if the form is valid
      validateAndUpdateRow() {
      
        // Proceeds if form is valid
        if (this.formValid) {
        if (this.editingIndex !== null && this.editingIndex >= 0) {
          
          // Update the task in the valueOfTasks array using the editingIndex
          const task = this.valueOfTasks[this.editingIndex];
          task.description = this.description;
          
          // Use pre-existing deadline if no date is entered
          if (this.date2 != "") {
            task.deadline = this.date2 ? new Date(this.date2).toLocaleDateString("en-US") : '';
          }
          task.priority = this.priorityButton;

        // Show the success snackbar
        this.updateSuccess = true;

        // Reset the form and close the update overlay
        this.resetUpdatedForm();
        }
      }
      else {
        this.formFailure = true;
      }
      },

      // Closes the initial datepicker when a selection is made
      onDateSelected() {
        this.showDatePicker = false;
      },

      // Shows the datepicker when called
      dateAdd() {
        this.showDatePicker = true;
      },

      // Closes the datepicker when a selection is made
      onUpdatedDateSelected() {
        this.showUpdatedDatePicker = false;
      },

      // Shows the datepicker when called
      updatedDateAdd() {
        this.showUpdatedDatePicker = true;
      },
      
      // Clears the form and closes the overlay
      resetForm() {
        this.add = false;
        this.title = '';
        this.description = '';
        this.deadline = "";
        this.date1 = null;
        this.priorityButton = 'Med';
        this.$refs.taskForm.resetValidation();
    },

      resetUpdatedForm() {
        this.update = false;
        this.title = '';
        this.description = '';
        this.deadline = '';
        this.priorityButton = '';
        this.editingIndex = null;
        this.$refs.taskForm.resetValidation();
    },
    },
    };

  </script>

  <style scoped>
.fill-height {
  height: 100vh; /* Makes the container take the full viewport height */
}
</style>