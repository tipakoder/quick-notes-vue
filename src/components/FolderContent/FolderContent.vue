<script>
    import OptionsListVue from "../OptionsList/OptionsList.vue";

    export default {
        name: "FolderContent",

        components: {
            OptionsListVue
        },

        props: [
            "selectedFolder"
        ],
        
        data () {
            return {
                folder: this.selectedFolder ? Object.assign({}, this.selectedFolder) : {
                    title: "",
                    description: "",
                    elements: []
                },
                optionsList: [
                    {
                        title: "Редактировать",
                        click: () => {
                            this.editingMode = !this.editingMode;
                        }
                    },
                    {
                        title: "Удалить",
                        color: "red"
                    }
                ],
                contextMenuVisible: false,
                editingMode: false
            }
        },

        watch: {
            selectedFolder: function (newValue) {
                if (newValue !== null && newValue !== this.folder) {
                    newValue.title = newValue.title.trim();
                    newValue.description = newValue.description.trim();

                    this.editingMode = false;

                    this.folder = Object.assign({}, newValue);
                }

                this.hideContextMenu();
            } 
        },

        methods: {
            updateFolderData () {
                this.$emit('updateSelectedFolder', this.folder);
            },

            applyEditing () {
                this.updateFolderData();
                this.editingMode = false;
                this.hideContextMenu();
            },

            cancelEditing () {
                this.folder = Object.assign({}, this.selectedFolder);
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
            />
        </template>

        <template
            v-if="!editingMode"
        >
            <h2>{{folder.title}}</h2>
            <p>{{folder.description}}</p>
        </template>
    </div>

    <div
        v-if="selectedFolder === null"
        class="folder-placeholder"
    >
        <p class="text">Выберите интересующий раздел</p>
    </div>
</template>

<style>
    #folder-menu {
        position: fixed;
        top: 22px;
        right: 22px;
    }

    #folder-menu > .context-menu {
        position: absolute;
        top: 48px;
        right: 0;
    }

    #folder-menu > .folder-menu-options {
        display: flex;
        align-items: center;
        justify-content: end;
        grid-gap: 16px;

        background-color: transparent;
    }

    #folder-menu > .folder-menu-options > .action-button {
        padding: 4px 8px;

        cursor: pointer;

        color: #FFF;
        border-radius: 8px;
    }

    #folder-menu > .folder-menu-options > .apply {
        background-color: var(--main-accent);
    }

    #folder-menu > .folder-menu-options > .cancel {
        background-color: var(--red-modify);
    }

    #folder-menu .icon {
        width: 32px;
        height: 32px;

        border-radius: 100px;
        cursor: pointer;

        transition: background-color 0.2s;
    }

    #folder-menu.visible .icon {
        background-color: var(--main-bg-light-gray);
    }

    .folder-placeholder {
        position: fixed;
        top: 0;
        bottom: 0;
        left: 300px;
        right: 0;

        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;

        background-color: var(--main-bg-dark-gray);
    }

    .folder-placeholder > .text {
        padding: 4px 16px;

        font-weight: 500;
        font-size: 14px;
        
        color: #FFF;
        border-radius: 80px;
        background-color: var(--main-bg-dim-gray);
    }

    .folder-content {
        position: fixed;
        top: 0;
        bottom: 0;
        left: 300px;
        right: 0;

        display: flex;
        flex-direction: column;
        grid-gap: 16px;

        padding: 36px;

        color: #FFF; 
        background-color: var(--main-bg-dark-gray);
    }

    .folder-edit-input {
        border: 0;
        background-color: transparent;

        resize: none;

        font-weight: 400;
        font-size: 14px;
        color: #FFF;        
    }

    textarea.folder-edit-input {
        height: 100%;
    }

    .folder-edit-input.empty {
        caret-color: transparent;
    }

    .folder-edit-input::placeholder {
        font-weight: 400;
        font-size: 14px;
        color: #FFF;
    }

    .folder-edit-input.title {
        font-size: 24px;
    }

    .folder-edit-input.title::placeholder { 
        font-size: 24px;
    }
</style>