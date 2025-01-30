<template>
  <div>
    <ejs-dropdownbutton
      v-if="!showConfirmDialog"
      class="bs-template-merge"
      id="merge_button"
      :items="mergeOptions"
      :content="mergeAndUse"
      :open="setPopoverPosition"
      @select="mergeOptionsSelect"
    ></ejs-dropdownbutton>

    <!-- Delete Confirmation Dialog -->
    <ejs-dialog
      ref="deleteConfirmDialog"
      :isModal="true"
      :buttons="deleteDialogButtons"
      :visible="deleteDialogVisible"
      width="450px"
      cssClass="confirm-delete-template-dialog"
    >
      <div class="flex items-start pb-4 pt-4 pl-3 pr-8">
        <div class="warning-gradient-bg"></div>
        <div class="text-content-style">
          {{ $nuxt.$t('templates.confirmPermanentDelete') }}
        </div>
      </div>
    </ejs-dialog>
  </div>
</template>

<script>
import { Component, Vue } from "vue-property-decorator";

@Component
export default class MergeTemplateDropDown extends Vue {
  deleteDialogVisible = false;

  mergeOptions = [
    { text: "Create document", id: "mergeDoc" },
    { text: "Create bulk link", id: "mergeLink" },
    { text: "Delete", id: "mergedelete" },
  ];

  mergeAndUse = "Merge & Use";

  mergeOptionsSelect(args) {
    if (args.item.id === "mergeDoc") {
      this.$nuxt.$emit("mergeTemplate", "document");
    } else if (args.item.id === "mergedelete") {
      this.deleteDialogVisible = true; // Show delete dialog
    } else {
      this.$nuxt.$emit("mergeTemplate", "bulklink");
    }
  }

  setPopoverPosition() {
    const dropdown = document.querySelector("#merge_button");
    const popover = document.querySelector("#merge_button-popup");
    if (dropdown && popover) {
      popover.style.left = dropdown.getBoundingClientRect().left + "px";
    }
  }

  deleteYesBtn() {
    this.$nuxt.$spinner.show(true);
    const gridObj = this.$refs.grid1.ej2Instances;
    const selectedRecords = gridObj.getSelectedRecords();
    const docID = selectedRecords[0].documentId;
    const successContent = this.$nuxt.$t("toast.deleteTempPermanentSuccessToastTittle");
    const failureContent = this.$nuxt.$t("toast.deleteTempPermanentFailureToastTittle");

    this.$api.template
      .deleteTemplate(docID)
      .then(() => {
        this.$nuxt.$toastService.show(successContent, "bs_toast_success", "bs_delete_toast");
        this.$nuxt.$spinner.show(false);
      })
      .catch(() => {
        this.$nuxt.$toastService.show(failureContent, "bs_toast_failure", "bs_delete_toast");
        this.$nuxt.$spinner.show(false);
      });

    this.deleteDialogVisible = false;
  }
}
</script>
