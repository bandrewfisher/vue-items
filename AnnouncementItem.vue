<template>
    <li>
        <div id="editLinksDiv">
            <a @click="$emit('edit', content)">Edit</a>
            <a>Copy</a>
            <a>Recycle</a>
        </div>

        <div id="announcementMainContentDiv">
            <h3>{{ content.subject }}</h3>
            <p v-html="content.content"></p>
        </div>

        <div id='instructorInfoDiv'>
            <p><strong>{{ content.instructorName }}</strong></p>
            <p>{{content.courses}}</p>
            <!--p>{{ content.availableDate }}
                <span v-if="content.hasExp">
                    to {{content.expDate}}
                </span>
            </p-->
        </div>

        
    </li>
</template>

<script>
    define(['Vue', 'underscore'], function(Vue, _) {
        return {
            props: ['content', 'newContent', 'edit', 'instructorName'],
        }
    });
</script>

<style scoped>
li {
    border-bottom: 1px solid #eee;
    margin-bottom: 0.75em;
    min-height: 3.75em;
    overflow: hidden;
}
div {
    display: inline-block;
}

#instructorInfoDiv {
    float: right;
    width: 14em;
    word-wrap: break-word;
}

#instructorInfoDiv p{
    margin-bottom: 0.125em;
    margin-top: 0.125em;
}

#editLinksDiv a {
    display: block;
}

#announcementMainContentDiv {
    margin-left: 1em;
}
</style>
