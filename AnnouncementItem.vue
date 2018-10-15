<template>
    <div id="itemContainerDiv">
        <div id="editLinksDiv">
            <a @click="$emit('edit', content)">Edit</a>
            <a>Copy</a>
            <a>Recycle</a>
        </div>

        <div id="announcementMainContentDiv">
            <h3><span class="annSubject" >{{ content.subject }}</span></h3>
            <p v-html="content.content"></p>
        </div>

        <div id='instructorInfoDiv'>
            <p><strong>{{ content.instructorName }}</strong></p>
            <p>{{content.courses}}</p>
            <p>{{ content.availableDate}}</p>
            <p>{{ content.expDate }}</p>
            <!--p>{{ content.availableDate }}
                <span v-if="content.hasExp">
                    to {{content.expDate}}
                </span>
            </p-->
        </div>

        
    </div>
</template>

<script>
    define(['Vue', 'underscore'], function(Vue, _) {
        return {
            props: ['content', 'newContent', 'edit', 'instructorName'],
        }
    });
</script>

<style scoped>
#itemContainerDiv {
    border-bottom: 1px solid #eee;
    display: block;
    width: 100%;
    margin-bottom: 0.75em;
    min-height: 3.75em;
    overflow: hidden;
}
div {
    display: inline-block;
}

.annSubject:hover {
    cursor: pointer;
}

#instructorInfoDiv {
    float: right;
    width: 14em;
    word-wrap: break-word;
    margin-right: 2.5em;
    font-size: 12px;

}

#instructorInfoDiv p{
    margin-bottom: 0.225em;
    margin-top: 0.125em;
    color: #888;
}

#editLinksDiv a {
    display: block;
}

#announcementMainContentDiv {
    margin-left: 1em;
}
</style>
