# Any_URL_in_New_Tab
Open any URL in a new Tab. URL coming from TVARVC table

//For Metadata/Model/SEGW, use metadata.xml

////Gateway Coding
  METHOD /iwbep/if_mgw_appl_srv_runtime~execute_action.

    DATA ls_summary       TYPE          zcl_zfiori_legacy_view_mpc=>ts_tilecontent.
    DATA: lv_tvarvc_name TYPE rvari_vnam,
          lv_string TYPE string.

    DATA: lv_fi_name TYPE /iwbep/mgw_tech_name.
    DATA: ls_parameter  TYPE /iwbep/s_mgw_name_value_pair.
    DATA: lt_parameters TYPE /iwbep/t_mgw_name_value_pair.

    lt_parameters = io_tech_request_context->get_parameters( ).

    lv_fi_name = io_tech_request_context->get_function_import_name( ).
    CASE lv_fi_name.
      WHEN 'Summary'.

        "Get PARAMETER1
        READ TABLE lt_parameters INTO ls_parameter WITH KEY name = 'PARAMETER1'.
        IF sy-subrc = 0.
          lv_tvarvc_name = ls_parameter-value.
          IF lv_tvarvc_name IS NOT INITIAL.
            " Get TVARVC value
            SELECT SINGLE low INTO ls_summary-backendhost FROM tvarvc WHERE name = lv_tvarvc_name.
          ENDIF.
        ENDIF.

        "Get PARAMETER2
        CLEAR ls_parameter.
        READ TABLE lt_parameters INTO ls_parameter WITH KEY name = 'PARAMETER2'.
        IF sy-subrc = 0.
          lv_tvarvc_name = ls_parameter-value.
          IF lv_tvarvc_name IS NOT INITIAL.
            " Get TVARVC value
            SELECT SINGLE low INTO lv_string FROM tvarvc WHERE name = lv_tvarvc_name.
            IF sy-subrc EQ 0.
              CONCATENATE ls_summary-backendhost lv_string INTO ls_summary-backendhost.
            ENDIF.
          ENDIF.
        ENDIF.

        "Get PARAMETER3
        CLEAR ls_parameter.
        READ TABLE lt_parameters INTO ls_parameter WITH KEY name = 'PARAMETER3'.
        IF sy-subrc = 0.
          lv_tvarvc_name = ls_parameter-value.
          IF lv_tvarvc_name IS NOT INITIAL.
            " Get TVARVC value
            SELECT SINGLE low INTO lv_string FROM tvarvc WHERE name = lv_tvarvc_name.
            IF sy-subrc EQ 0.
              CONCATENATE ls_summary-backendhost lv_string INTO ls_summary-backendhost.
            ENDIF.
          ENDIF.
        ENDIF.


      WHEN OTHERS.

    ENDCASE.

    CALL METHOD copy_data_to_ref
      EXPORTING
        is_data = ls_summary
      CHANGING
        cr_data = er_data.

  ENDMETHOD.