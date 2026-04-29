CREATE TABLE seasmgr.t_helth_msds_mng (
    tenant_id varchar(100) NOT NULL,
    msdsobj_uid bpchar(40) NOT NULL,
    mat_nm varchar(400) NOT NULL,
    sup_nm varchar(400) NOT NULL,
    matobj_txt_cntn text NULL,
    atth_doc_id varchar(100) NULL,
    fst_rgst_dtm bpchar(14) NOT NULL,      -- thời gian tạo (YYYYMMDDHHMMSS)
    fst_rgst_usr_id varchar(100) NOT NULL, -- người tạo
    fst_rgst_prg_id varchar(100) NOT NULL, -- chương trình tạo
    fnl_mdfc_dtm bpchar(14) NOT NULL,      -- thời gian sửa cuối
    fnl_mdfc_usr_id varchar(100) NOT NULL, -- người sửa cuối
    fnl_mdfc_prg_id varchar(100) NOT NULL, -- chương trình sửa
    CONSTRAINT pk_helth_msds_mng PRIMARY KEY (tenant_id, msdsobj_uid)
);
