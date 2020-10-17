<script>
import axios from "axios";
import _ from "lodash";

import vue2Dropzone from "vue2-dropzone";
import Multiselect from "vue-multiselect";

import Layout from "../../layouts/main";
import PageHeader from "../../components/page-header";

/**
 * Add-product component
 */
export default {
    components: {
        vueDropzone: vue2Dropzone,
        Multiselect,
        Layout,
        PageHeader,
    },
    data() {
        return {
            title: "Add Product",
            items: [{
                    text: "Ecommerce",
                    href: "/",
                },
                {
                    text: "Add Product",
                    active: true,
                },
            ],
            dropzoneOptions: {
                url: "https://httpbin.org/post",
                thumbnailHeight: 100,
            },
            value: null,
            value1: null,
            options: [
                "Alaska",
                "Hawaii",
                "California",
                "Nevada",
                "Oregon",
                "Washington",
                "Arizona",
                "Colorado",
                "Idaho",
                "Montana",
                "Nebraska",
                "New Mexico",
                "North Dakota",
                "Utah",
                "Wyoming",
                "Alabama",
                "Arkansas",
                "Illinois",
                "Iowa",
            ],
            formData: {
                name: null,
                manufacture_name: null,
                manufacture_brand: null,
                price: null,
                image: null,
            },
            avatar: null,
            avatarName: null,
            showForm: true,
            user: null,
            errors: null,
        };
    },
    methods: {
        fileAdded(file) {
            this.avatar = file;
            this.avatarName = file.name;
        },
        submit() {
            this.errors = null;

            let formData = new FormData();
            formData.append("image", this.avatar, this.avatarName);
            _.each(this.formData, (value, key) => {
                formData.append(key, value);
            });

            axios
                .post("/api/products", formData, {
                    headers: {
                        "Content-Type": "multipart/for-data",
                    },
                })
                .then((response) => {
                    this.showForm = false;
                    this.user = response.data.data;
                })
                .catch((err) => {
                    if (err.response.status === 422) {
                        this.errors = [];
                        _.each(err.response.data.errors, (error) => {
                            _.each(error, (e) => {
                                this.errors.push(e);
                            });
                        });
                    }
                });
        },
    },
};
</script>

<template>
<Layout>
    <PageHeader :title="title" :items="items" />
    <div class="row">
        <div class="col-12">
            <div class="card">
                <div class="card-body">
                    <h4 class="card-title">Basic Information</h4>
                    <p class="card-title-desc">Fill all information below</p>

                    <!-- THIS SECTION IS FOR ERRORS THAT WOULD COME FROM API -->
                    <div v-if="errors">
                        <div v-for="(error, index) in errors" :key="index" class="alert alert-danger">
                            {{ error }}
                        </div>
                    </div>
                    <form enctype="multipart/form-data" method="POST">
                        <div class="row">
                            <div class="col-sm-6">
                                <div class="form-group">
                                    <label for="productname">Product Name</label>
                                    <input id="productname" name="name" type="text" class="form-control" v-model="formData.name" />
                                </div>
                                <div class="form-group">
                                    <label for="manufacturername">Manufacturer Name</label>
                                    <input id="manufacturername" name="manufacturername" type="text" class="form-control" v-model="formData.manufacture_name" />
                                </div>
                                <div class="form-group">
                                    <label for="manufacturerbrand">Manufacturer Brand</label>
                                    <input id="manufacturerbrand" name="manufacturerbrand" type="text" class="form-control" v-model="formData.manufacture_brand" />
                                </div>
                                <div class="form-group">
                                    <label for="price">Price</label>
                                    <input id="price" name="price" type="text" class="form-control" v-model="formData.price" />
                                </div>
                                <div class="form-group">
                                    <label for="price">Drop</label>
                                    <vue-dropzone id="dropzone" ref="file" name="image" :use-custom-slot="true" :options="dropzoneOptions" @vdropzone-file-added="fileAdded">
                                        <div class="dropzone-custom-content">
                                            <div class="mb-1">
                                                <i class="display-4 text-muted bx bxs-cloud-upload"></i>
                                            </div>
                                            <h4>Drop files here or click to upload.</h4>
                                        </div>
                                    </vue-dropzone>
                                </div>
                            </div>

                            <div class="col-sm-6">
                                <div class="form-group">
                                    <label class="control-label">Category</label>
                                    <multiselect v-model="value" :options="options"></multiselect>
                                </div>
                                <div class="form-group">
                                    <label class="control-label">Features</label>
                                    <multiselect v-model="value1" :options="options" :multiple="true"></multiselect>
                                </div>
                                <div class="form-group">
                                    <label for="productdesc">Product Description</label>
                                    <textarea id="productdesc" class="form-control" rows="5"></textarea>
                                </div>
                            </div>
                        </div>

                        <button type="submit" @click.prevent="submit" class="btn btn-primary mr-1">
                            Save Changes
                        </button>
                        <button type="submit" class="btn btn-secondary">Cancel</button>
                    </form>
                </div>
            </div>

            <div class="card">
                <div class="card-body">
                    <h4 class="card-title">Meta Data</h4>
                    <p class="card-title-desc">Fill all information below</p>

                    <form>
                        <div class="row">
                            <div class="col-sm-6">
                                <div class="form-group">
                                    <label for="metatitle">Meta title</label>
                                    <input id="metatitle" name="productname" type="text" class="form-control" />
                                </div>
                                <div class="form-group">
                                    <label for="metakeywords">Meta Keywords</label>
                                    <input id="metakeywords" name="manufacturername" type="text" class="form-control" />
                                </div>
                            </div>

                            <div class="col-sm-6">
                                <div class="form-group">
                                    <label for="metadescription">Meta Description</label>
                                    <textarea id="metadescription" class="form-control" rows="5"></textarea>
                                </div>
                            </div>
                        </div>

                        <button type="submit" class="btn btn-primary mr-1">
                            Save Changes
                        </button>
                        <button type="submit" class="btn btn-secondary">Cancel</button>
                    </form>
                </div>
            </div>
        </div>
    </div>
    <!-- end row -->
</Layout>
</template>
