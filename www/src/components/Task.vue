<template>
    <draggable :options="{group: 'tasks'}" @end ="moveTaskToDifferentList(lists[this._id])">
        <div class="comment-container">
            <div class="tasks open-comments">
                <p class="taskTitle" @click="commentsSeen = !commentsSeen"><b>Task: </b>{{name}}
                    <i class="fa fa-trash float" @click="removeTask"></i>
                    <div class="comments" v-if="commentsSeen" v-for="comment in comments">
                        <p>{{comment.description}}
                            <i class="fa fa-minus" @click="removeComment(comment._id)"></i>
                        </p>
                    </div>
                    <p class="add-comment" v-if="commentsSeen" @click="toggleCommentForm">Add Comment <span class="fa fa-plus"></span> </p>
                </p>
            </div>

            <div class="commentForm" v-if="showCommentForm">
                <!-- <div class="cformHead">
                <h4>New Comment</h4>
            </div> -->
                <div class="newComment">
                    <form @submit.prevent="newComment">
                        <div class="form-group">
                            <input placeholder="comment" name="description" type="text" size="10" v-model="comment.description">
                            <button class="btn-success btn-xs" type="submit">Add</button>
                        </div>
                    </form>
                </div>
            </div>
            <div class="the-lists">
                <form @change="moveTaskToDifferentList">
                    <select name="select list" v-model="formOption">
                        <option disabled selected>Select List</option>
                        <option v-for="list in lists" :value="list._id">{{list.name}}</option>
                    </select>
                </form>
            </div>
        </div>
    </draggable>

</template>

<script>
    import draggable from 'vuedraggable'
    import list from './List'
    export default {
        data() {
            return {
                showCommentForm: false,
                comment: {
                    boardId: this.boardId,
                    listId: this.listId,
                    taskId: this.taskId
                },
                formOption: '',
                commentsSeen: false
            }
        },
        name: 'task',
        props: ['name', 'description', 'taskId', 'listId', 'boardId', 'newListId'],
        mounted() {
            this.$store.dispatch('getComments', { taskId: this.taskId, listId: this.listId, boardId: this.boardId })
        },
        methods: {
            openComments() {
                this.$store.dispatch('getComments', { taskId: this.taskId, listId: this.listId, boardId: this.boardId })
            },
            moveTaskToDifferentList(newListId) {
                
                this.$store.dispatch('moveTaskToDifferentList', { taskId: this.taskId, boardId: this.boardId, oldListId: this.listId, listId: this.formOption })
            },
            newComment() {
                this.$store.dispatch('newComment', { comment: this.comment })
                this.comment = {
                    boardId: this.boardId,
                    listId: this.listId,
                    taskId: this.taskId
                    
                }
                this.toggleCommentForm()
            },
            removeTask() {
                this.$store.dispatch('removeTask', { taskId: this.taskId, listId: this.listId, boardId: this.boardId })
            },
            removeComment(commentId) {
                this.$store.dispatch('removeComment', { taskId: this.taskId, listId: this.listId, boardId: this.boardId, commentId: commentId })
            },
            toggleCommentForm() {
                this.showCommentForm = !this.showCommentForm
            }
        },
        computed: {
            lists() {
                return this.$store.state.activeLists
            },
            tasks() {
                return this.$store.state.activeTasks
            },
            comments() {
                return this.$store.state.activeComments[this.taskId]
            }
        },
        components: {
            draggable
        }

    }
</script>

<style scoped>
    .fa-ban {
        color: red;
        float: right;
    }
    .fa-plus {
        color: rgb(13, 173, 26);
    
    }
    .open-comments {
        /* color: white; */
        cursor: pointer;
        /* font-size: 100%;  */
    }

    .fa-minus {
        color: red;
    }
    .fa-trash:hover{
        color: rgb(255, 137, 137);
    }
    .fa-minus:hover{
        color: rgb(255, 137, 137);
    }
    .comment-container {
        margin-left: 2rem;
        margin-right: 2rem;
        border: 1px solid black;
        border-radius: 5px;
        /* background: pink; */
        background: rgb(252, 185, 185);
        padding: 1rem;
    }

    .add-comment {
        /* color: white; */
        padding-top: 5%;
        cursor: pointer;
    }
    .add-comment:hover{
        color: rgb(7, 7, 7);
    }
    .taskTitle:hover{
        color: rgb(2, 2, 2);
    }
    .comments {
        margin: 2rem;
        border: 1px solid black;
        border-radius: 5px;
        background: rgb(247, 250, 247);
        word-wrap: normal;
        padding-top: .5rem;
    }
    .float{
        float: right;
    }

    /* .tasks{
        margin-top: 0.5rem;
    } */
</style>