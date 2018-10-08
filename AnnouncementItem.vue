<template>
    <li>
        <div>
            <h3>{{ content.subject }}</h3>
            <p v-html="content.content"></p>
        </div>

        <div>
            <p><strong>{{ instructorName }}</strong></p>
            <p>{{ content.availableDate }}</p>
        </div>

        <div>
            <a @click="$emit('edit', content)">Edit</a>
            <a>Copy</a>
            <a>Recycle</a>
        </div>
    </li>
</template>

<script>
    define(['Vue', 'underscore'], function(Vue, _) {
        return {
            props: ['content', 'edit', 'instructorName'],
        }
    });
</script>
