<script>
    import FolderMenuVue from "./components/FolderList/FolderMenu.vue";
    import FolderContentVue from "./components/FolderContent/FolderContent.vue";

    export default {
        name: "ApplicationScheme",
        
        components: {
            FolderMenuVue,
            FolderContentVue
        },

        created() {
            document.addEventListener('keydown', this.shortCuts);

            if (localStorage.getItem("foldersList")) {
                try {
                    this.folders = JSON.parse(localStorage.getItem("foldersList")) ?? [];
                } catch (e) {
                    this.folders = [];
                }
            }
        },

        data () {
            return {
                selectedFolder: null,
                editingMode: false,
                folders: []
            }
        },

        methods: {
            shortCuts (e) {
                // ESCAPE SHORCUT
                if (e.keyCode == 27) {
                    this.unselectFolder();
                }
            },

            unselectFolder () {
                if (
                    this.selectedFolder.title === "" && 
                    this.selectedFolder.description === ""
                ) {
                    const selectedFolderIndex = this.folders.indexOf(this.selectedFolder);
                    this.folders.splice(selectedFolderIndex, 1);
                }

                this.selectedFolder = null;
            },

            selectFolder (folder) {
                this.editingMode = false;
                this.selectedFolder = folder;

                console.log(this.editingMode);
            },

            createFolder () {
                const newFolder = {
                    title: "",
                    description: "",
                    elements: []
                }

                this.folders.push(newFolder);
                this.selectFolder(newFolder);
                this.editingMode = true;
            },

            updateSelectedFolder (updatedFolder) {
                const selectedFolderIndex = this.folders.indexOf(this.selectedFolder);

                for (let key in updatedFolder) {
                    if (this.folders[selectedFolderIndex][key] !== updatedFolder[key]) {
                        this.folders[selectedFolderIndex][key] = updatedFolder[key];
                    }
                }

                this.save();
            },

            save () {
                localStorage.setItem("foldersList", JSON.stringify(this.folders));
            }
        }
    };
</script>

<style>
    @import url("@/assets/styles/main.css");

    .application-wrapper {
        position: fixed;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;

        background-color: var(--main-bg-dark-gray);
    }
</style>

<template>
    <div class="application-wrapper">
        <FolderMenuVue
            :folders="folders"
            :selectedFolder="selectedFolder"
            @setSelectedFolder = "selectFolder($event)"
            @createFolder = "createFolder" 
        />

        <FolderContentVue 
            :selectedFolder="selectedFolder"
            :editingModeIndicator="editingMode"
            @updateSelectedFolder = "updateSelectedFolder($event)"
        />
    </div>
</template>