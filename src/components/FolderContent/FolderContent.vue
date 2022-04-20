<script>
    import OptionsListVue from "../OptionsList/OptionsList.vue";

    export default {
        name: "FolderContent",

        components: {
            OptionsListVue
        },

        props: [
            "selectedFolder",
            "editingModeIndicator"
        ],
        
        data () {
            return {
                folder: this.selectedFolder ? Object.assign({}, this.selectedFolder) : {
                    title: "",
                    description: "",
                    elements: []
                },
                folderBeforeEditing: null,
                optionsList: [
                    {
                        title: "Редактировать",
                        click: () => {
                            this.folderBeforeEditing = Object.assign({}, this.folder);
                            this.editingMode = !this.editingMode;
                        }
                    },
                    {
                        title: "Удалить",
                        color: "red",
                        click: () => {
                            this.$emit('removeFolder', this.selectedFolder);
                            this.$emit('unselectFolder');
                        }
                    }
                ],

                elementsVariors: [
                    {
                        title: "Параграф",
                        click: this.addingParagraph
                    },
                    {
                        title: "Картинка",
                        click: () => {

                        }
                    },
                    {
                        title: "Календарь",
                        click: () => {

                        }
                    },
                    {
                        title: "Раздел",
                        click: () => {

                        }
                    }
                ],

                contextMenuVisible: false,
                contextAddingVisible: false,
                editingMode: false
            }
        },

        watch: {
            selectedFolder: function (newValue) {
                this.editingMode = false;

                if (newValue !== null && newValue !== this.folder) {
                    newValue.title = newValue.title.trim();
                    newValue.description = newValue.description.trim();

                    this.folder = Object.assign({}, newValue);
                }

                this.hideContextMenu();
            },

            editingModeIndicator: function(value) {
                if (value) {
                    this.folderBeforeEditing = Object.assign({}, this.folder);
                }

                this.editingMode = value;
            },

            editingMode: function() {
                for(let target of document.getElementsByTagName("textarea")) {
                    console.log(target.scrollHeight);
                    target.style.height = 'auto';
                    target.style.height = `${target.scrollHeight}px`;
                }
            }
        },

        methods: {
            updateFolderData () {
                this.$emit('updateSelectedFolder', this.folder);
            },

            applyEditing () {
                this.updateFolderData();
                this.folderBeforeEditing = null;
                this.editingMode = false;
                this.hideContextMenu();
            },

            cancelEditing () {
                this.folder = Object.assign({}, this.folderBeforeEditing);
                this.folderBeforeEditing = null;
                this.editingMode = false;
                this.hideContextMenu();
            },

            toggleContextMenu () {
                this.contextMenuVisible = !this.contextMenuVisible;
            },

            showContextMenu () {
                this.contextMenuVisible = true;
            },

            hideContextMenu () {
                this.contextMenuVisible = false;
            },

            textareaResize(e) {
                e.target.style.height = 'auto';
                e.target.style.height = `${e.target.scrollHeight}px`;
            },

            // Добавление элементов раздела
            // Параграф
            addingParagraph () {
                this.folder.elements.push(
                    {
                        tag: "p",
                        text: ""
                    }
                );
            }
        }

    }
</script>

<template>
    <div 
        v-if="selectedFolder !== null"
        class="folder-content"
    >
        <div
            id="folder-menu"
            :class="{
                visible: contextMenuVisible
            }"
        >
            <div class="folder-menu-options">
                <button 
                    v-if="editingMode"
                    class="action-button apply"
                    @click="applyEditing"
                >Применить</button>

                <button 
                    v-if="editingMode"
                    class="action-button cancel"
                    @click="cancelEditing"
                >Отменить</button>

                <img 
                    class="icon"
                    @click="toggleContextMenu"
                    src="@/assets/icons/points_menu.svg"
                >    
            </div>    

            <OptionsListVue 
                v-if="contextMenuVisible"
                :optionsList="optionsList"
                @click="hideContextMenu"
                class="context-menu"
            />
        </div>        

        <template
            v-if="editingMode"
        >
            <input  
                type="text" 
                placeholder="Заголовок"
                :class="{
                    empty: this.folder.title === ''
                }"
                class="folder-edit-input title"
                v-model="folder.title"
            >
            <textarea 
                placeholder="Описание"
                :class="{
                    empty: this.folder.description === ''
                }"
                class="folder-edit-input"
                v-model="folder.description"
                @input="this.textareaResize"
            ></textarea>

            <template 
                v-for="(element, id) in folder.elements"
                :key="id"
            >
                <div class="folder-element-editing">
                    <img
                        @click="folder.elements.splice(id, 1)"
                        src="@/assets/icons/remove.svg"
                        class="icon-remove"
                    >

                    <textarea
                        v-model="element.text"
                        class="input-edit-text"

                        @input="this.textareaResize"
                    />
                </div>
            </template>

            <div class="adding-elements-bar">
                <img 
                    @click="contextAddingVisible = true"
                    src="../../assets/icons/plus_small.svg"
                >

                <OptionsListVue 
                    v-if="contextAddingVisible"
                    :optionsList="elementsVariors"
                    @click="contextAddingVisible = false"
                    class="context-menu"
                />
            </div>
        </template>

        <template
            v-if="!editingMode"
        >
            <h2>{{folder.title}}</h2>
            <p>{{folder.description}}</p>

            <hr>

            <template 
                v-for="(element, id) in folder.elements"
                :key="id"
            >
                <p 
                    v-if="element.tag === 'p'"
                >{{element.text}}</p>
            </template>
        </template>
    </div>

    <div
        v-if="selectedFolder === null"
        class="folder-placeholder"
    >
        <p class="text">Выберите интересующий раздел</p>
    </div>
</template>

<style src="./folderContent.css"></style>