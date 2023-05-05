<script setup>

import { ref, onMounted, computed } from 'vue';

let searchQuery  = ref( '' );
let selectedItem = ref( null );
let isVisible    = ref( false );
let userArray    = ref( [] );

onMounted( () => {
    fetch( "https://jsonplaceholder.typicode.com/users" )
        .then( reponse => reponse.json() )
        .then( json => {
            userArray.value = json;
        } )
})

function selectItem( user ) {
    selectedItem.value = user;
    isVisible.value    = false;
}

const filteredUsers = computed( () => {
    const query = searchQuery.value.toLowerCase();
    if ( query === "" ) {
        return userArray.value;
    }

    return userArray.value.filter( ( user ) => {
        return Object.values( user ).some( ( word ) =>
            String( word ).toLowerCase().includes( query ) 
        );
    } );
})

</script>

<template>
    <div>
        <section class="dropdown-wrapper">
            <div @click="isVisible = !isVisible" class="selected-item">
                <span v-if="selectedItem">{{ selectedItem.name }}</span>
                <span v-else> Selected User </span>
                <svg 
                    :class="isVisible ? 'dropdown' : ''"
                    class="drop-down-icon"
                    xmlns="http://www.w3.org/2000/svg" 
                    viewBox="0 0 24 24"
                    width="24"
                    height="24"
                >
                    <path d="M11.9997 10.8284L7.04996 15.7782L5.63574 14.364L11.9997 8L18.3637 14.364L16.9495 15.7782L11.9997 10.8284Z"></path></svg>
            </div>
            <div :class="isVisible ? 'visible' : 'invisible'" class="dropdown-popover">
                <input v-model="searchQuery" type="text" placeholder="Search for User">
                <span v-if="filteredUsers.length == 0 "> No Data Available</span>
                <div class="options">
                    <ul>
                        <li @click="selectItem(user)" v-for="(user, index) in filteredUsers" :key="index">
                        {{ user.name }}
                        </li>
                    </ul>
                </div>
            </div>
        </section>   
    </div>
</template>

<style scoped lang="scss">
.dropdown-wrapper {
    max-width: 350px;
    position: relative;
    margin: 0 auto;
    .selected-item {
    height: 40px;
    border: 2px solid lightgray;
    border-radius: 5px;
    padding: 5px 10px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-size: 18px;
    font-weight: 600;
    }

    .drop-down-icon {
        transform: rotate( 0deg );
        transition: all 0.5s ease;
        &.dropdown {
            transform: rotate( 180deg );
        }
    }

    .dropdown-popover {
        position: absolute;
        border: 2px solid lightgray;
        top: 46px;
        left: 0px;
        right: 0px;
        background-color: #fff;
        max-width: 100%;
        padding: 10px;
        visibility: hidden;
        transition: all 0.5s linear;
        max-height: 0px;
        overflow: hidden;

        &.visible {
            visibility: visible;
            max-height: 450px;
        }
    }

    input {
        width: 90%;
        height: 30px;
        border: 2px solid lightgray;
        font-size: 16px;
        padding-left: 8px;
    }

    .options {
        width: 100%;

        ul {
            list-style: none;
            text-align: left;
            padding-left: 8px;
            max-height: 180px;
            overflow-y: scroll;
            overflow-x: hidden;       
        }

        li {
            width: 100%;
            border-bottom: 1px solid lightgray;
            padding: 10px;
            background-color: #f1f1f1;
            cursor: pointer;
            font-size: 16px;
            &:hover {
                background: #70878a;
                color: #fff;
                font-weight: bold;
            }
        }
    }
}
</style>
