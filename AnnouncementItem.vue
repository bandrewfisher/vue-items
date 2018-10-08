<template>
    <li>
    <table>
        <tbody>
            <tr>
                <td width="70%">
                    <h3>
                        <div>{{ announcement.title }}</div>   
                    </h3>
                        
                    <div>
                        <p v-html="announcement.text"></p>
                    </div>
                </td>

                <td class="info" width="20%">
                    <p class="instructor"><strong>{{ announcement.instructorName }}</strong></p>
                    <p>{{ announcement.availableDate }}</p>
                </td>
            </tr>
        </tbody>
    </table>

    <a @click="$emit('edit', announcement)">Edit</a>
    <a>Copy</a>
    <a>Recycle</a>
    </li>
</template>

<script>
    define(['Vue', 'underscore'], function(Vue, _) {
        return {
            props: ['announcement', 'edit'],
        }
    });
</script>
