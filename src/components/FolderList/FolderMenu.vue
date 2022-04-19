<script>
    export default {
        name: "FolderMenu",

        data () {
            return {
                searchText: ""
            }
        },

        props: [
            "folders",
            "selectedFolder"
        ]
    }
</script>

<style>
    .folders-list {
        position: fixed;
        top: 0;
        bottom: 0;
        left: 0;

        width: 300px;

        background-color: var(--main-bg-dim-gray);
    }

    .folders-list > .folders-controls {
        display: flex;
        align-items: center;
        grid-gap: 16px;
        
        padding: 10px 16px 10px 12px;

        border-bottom: 1px solid var(--main-second-gray);
    }

    .folders-controls > .search-input {
        padding: 8px 16px;
        
        font-size: 14px;
        font-weight: 400;

        color: #FFF;
        border-radius: 4px;
        background-color: var(--main-bg-light-gray);

        width: 100%;
    }

    .folders-controls > .search-input::placeholder { 
        color: #FFF;
        font-size: 14px;
        font-weight: 400;
    }

    .folders-controls > .control-button {
        cursor: pointer;
    }

    .folders-list > ul {
        padding: 0;
        list-style: none;
    }

    .folders-list > ul > li {
        padding: 16px 24px;

        color: #FFF;
        cursor: pointer;
        border-bottom: 1px solid var(--main-second-gray);
        background-color: var(--main-bg-dim-gray);

        transition: background-color 0.2s, border-color 0.2s;
    }

    .folders-list > ul > li > .title {
        font-weight: 500;
        font-size: 18px;
    }

    .folders-list > ul > li > .desciption {
        font-weight: 300;
        font-size: 14px;

        margin-top: 5px;
    }

    .folders-list > ul > li.selected {
        background-color: var(--main-accent);
        border-color: var(--main-accent);
    }
</style>

<template>
    <div class="folders-list">
        <div class="folders-controls">
            <input 
                type="text"
                class="search-input" 
                v-model="searchText"
                placeholder="Поиск" 
            >
            <img 
                class="control-button"
                src="@/assets/icons/plus.svg"
                @click="this.$emit('createFolder')"
            >
        </div>

        <ul>
            <template
                v-for="(folder, id) in folders"
            >
                <li 
                    v-if="folder.title !== '' || folder.description !== ''"
                    :class="{
                        selected: selectedFolder === folder
                    }"
                    @click="this.$emit('setSelectedFolder', folder)"
                    :key="id"
                >
                    <h3 
                        class="title"
                        v-if="folder.title !== ''"
                    >
                        {{
                            folder.title.trim().length > 20 ? 
                            folder.title.trim().substr(0, 20) + ".." 
                            : folder.title.trim()
                        }}
                    </h3>
                    <p 
                        class="description"
                        v-if="folder.description !== ''"
                    >
                        {{
                            folder.description.trim().length > 22 ? 
                            folder.description.trim().substr(0, 22) + ".." 
                            : folder.description.trim()
                        }}
                    </p>
                </li>
            </template>
        </ul>
    </div>
</template>