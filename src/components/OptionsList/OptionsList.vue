<script>
    export default {
        name: "OptionsList",

        props: [
            "optionsList"
        ]
    }
</script>

<template>
    <ul class="options-list">
        <li 
            v-for="(option, id) in optionsList"
            :key="id"
            :class="{
                red_modify: option.color === 'red'
            }"
            @click="option.click"
        >
            {{option.title}}
        </li>
    </ul>
</template>

<style>
    .options-list {
        list-style: none;

        min-width: 150px;

        border-radius: 8px;
        background-color: var(--main-bg-dim-gray);

        overflow: hidden;
    }

    .options-list > li {
        padding: 8px;

        font-size: 14px;
        text-align: center;

        color: #FFF;
        cursor: pointer;

        border-bottom: 1px solid var(--main-second-gray);

        transition: background-color 0.2s;
    }

    .options-list > li.red_modify{
        background-color: var(--red-modify);
    }

    .options-list > li.red_modify:hover{
        background-color: var(--red-modify-hover);
    }

    .options-list > li:hover {
        background-color: var(--main-second-gray);
    }

    .options-list > li:last-child {
        border-bottom: 0;
    }
</style>