```python
s = 'H_S_|_e_gImt-o_tdondFS_ta_nlv0PgOSuSKGyCen3s_td_:Oc|iuM__t_aiFCeLasaea|T:d_oStoo_te-osiaL35rdf}:Mppnym|F_itnhecfF4:_npdnohtke_|_Eped_bC_Kch|enu_ig_Daendw_Ren4IFShcrEiahit0Kr_maoese_ci_e5i|cnnr_itavtseneUelHe_n_crGtgblcn-eddaCrs_i|elddgyoonpa0trrshul3G_:_iuAe:LdS_p_:h_cltr_ffie:tr|n_P_3cdS_|oeu4es_7Ekm|iG_{ce|_gCe0aumt_ES-wns:doA_v_e|crM5kr-Mlh}fucAea_e_ecp__umriHsCeese_|_aaa|_lt1dnDrGleeTml_3do_ale_CM-CnrrIpafL:dBerl:chesIWelKserfuy_auygn3_{s__|ne|_te___anmees|1rlErs|edaVA_Ji|ag-rs_rfSrcey_sCO1ellc|:iRinfamCdo_c:GertDes|3_hn7si_rC_cLn0RnpaerMt|hcou_:|S:haaY|sJiaGtscn0ees_yaf-oampb_-u_ir}_Keepsre_eym_reM_x{_sl_iRt_sTsg|ce_nteadFnM_Rs_yH_AEtlTdocJphteI4yDk__n:otOPoo1siT_tw_i?Utoeeo:nm_riH2d_u|uuen:tnubhrtn|6fhc_r_deEune:_SnK5b|a_eerRaihkCh{ntFiTiRvmsi_taDe_LCtTrsnonteohtC_sochcaim|sThn_y71hihy|ek_e_fa}rW:raet_do-thed4_icaoe|-r3Crkfr|1a|glfeFen_|efrtEp_rn'
for n in range(1, 100):
    res = ''
    d = list(range(n*1))+list(range(n*3-1, n*2-1, -1))+list(range(n*2-1, n*1-1, -1))+list(range(n*3, n*4))
    for st in d:
        for j in range(st, len(s), n*4):
            res += s[j]
    if 'tctf' in res: print(res); break
```

Все просто!
