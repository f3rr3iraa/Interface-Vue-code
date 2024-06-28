<template>
    <Vueform ref="form$" :display-errors="false" v-model="recordData" sync>
      <GroupElement name="container">
        <S2iDrawerSaveCancel :editingID="editingID" @iuRecord="iuRecord" @closeModal="$emit('closeModal')" />
        <GroupElement name="content">
          <DateElement name="docdate" :label="$t('docdate')" :rules="['required']"
            :columns="{ default: 12, sm: 12, md: 4, lg: 4 }"/>
          <DateElement name="expiredate" :label="$t('expiredate')" :rules="['required']"
            :columns="{ default: 12, sm: 12, md: 4, lg: 4 }" :min="new Date().toJSON().slice(0, 10)" />
          <DateElement name="warningdate" :label="$t('warningdate')" :rules="['required']"
            :columns="{ default: 12, sm: 12, md: 4, lg: 4 }" :min="new Date().toJSON().slice(0, 10)" />
          <GroupElement name="content">
  
            <TextElement name="remarks" :label="$t('remarks') + ' *'" :columns="{ default: 12, sm: 12, md: 6, lg: 6 }"
              :rules="['required']" />
  
            <SelectElement name="typeid" :label="$t('type') + ' *'" :columns="{ default: 12, sm: 6, md: 6, lg: 6 }"
              :items="auxType" valueProp="typeid" labelProp="type" :native="false" :caret="true" :rules="['required']" />
  
            <CheckboxElement name="imagebit" :label="$t('imagebit')" :columns="{ default: 12, sm: 6, md: 6, lg: 4 }"
              text="Sim" />
  
          </GroupElement>
        </GroupElement>
      </GroupElement>
    </Vueform>
  </template>
  
  <script>
    import S2iDrawerSaveCancel from "@/components/s2i/S2iDrawerSaveCancel.vue";
  
    export default {
      name: "MediaHeader",
      components: {
        S2iDrawerSaveCancel,
      },
      props: [
        'editingID',
        'curRecord',
      ],
      data() {
        return {
          recordData: [],
          auxType: []
        }
      },
      methods: {
  
        async fetchType() {
          this.auxType = (await this.execRoute(import.meta.env.VITE_APP_API_URL + "/v1/medias/types", null, "GET")).data
        },
  
        async iuRecord() {
          await this.$refs.form$.validate()
  
          if (!this.$refs.form$.invalid) {
            let bodycontent = {
              "pRemarks": this.recordData.remarks,
              "pImageBit": this.recordData.imagebit,
              "pTypeID": this.recordData.typeid,
              "pDocDate": this.recordData.docdate,
              "pExpireDate": this.recordData.expiredate,
              "pWarningDate": this.recordData.warningdate
  
            };
  
            let _res = await this.execRoute(import.meta.env.VITE_APP_API_URL + "/v1/equipments/" + this.$route.params.id + "/equipmentmedias/" + this.editingID, bodycontent, "POST");
  
            if (_res.result == "OK") {
              this.$emit('closeModal')
            }
          }
        }
      },
      async created() {
  
        await this.fetchType()
        if (this.editingID != 0) {
          this.recordData = this.curRecord;
        }
      }
    }
  </script>