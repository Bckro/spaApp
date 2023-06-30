<template>
    <div>
        Dashboard component
        <div v-if="user">Zalogowany użytkownik: {{ user }}</div>
        <div>
            <label>
                <input type="radio" v-model="component" value="list" />
                Lista
            </label>
            <label>
                <input type="radio" v-model="component" value="editor" />
                Edytor
            </label>
        </div>
        <component :is="selectedComponent" :student="student" :students="students" @create="createStudent"
            @edit="editStudent" @delete="deleteStudent" @submit="submitForm"></component>
    </div>
</template>
<script>
import StudentForm from "../components/StudentForm.vue";
import StudentsList from "../components/StudentsList.vue";
export default {
    inject: ["auth", "studentRepository"],
    components: { StudentForm, StudentsList },
    async created() {
        this.students = (await this.studentRepository.getAll()).data.data;
        this.user = (await this.auth.getUser()).data.name;
    },
    data() {
        return {
            user: null,
            component: "list",
            student: null,
            students: []
        };
    },
    methods: {
        createStudent() {
            this.student = null;
            this.component = "editor";
        },
        editStudent(st) {
            this.student = st;
            this.component = "editor";
        },
        async deleteStudent(st) {
            await this.studentRepository.delete(st.id);
            let index = this.students.findIndex(s => s.id == st.id);
            this.student = null;
            this.students.splice(index, 1);
        },
        async submitForm(st) {
            if (this.student?.id) {
                await this.studentRepository.update(this.student.id, st);
                let index = this.students.findIndex(s => s.id == this.student.id);
                this.students[index] = Object.assign(st, { id: this.student.id });
            } else {
                let createdStudent = (await this.studentRepository.add(st)).data.data;
                this.students.push(createdStudent);
                this.student = createdStudent;
            }
            this.component = "list";
        }
    },
    computed: {
        selectedComponent() {
            return this.component == "list" ? StudentsList : StudentForm;
        }
    }
};
</script>