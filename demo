TABLES spfli.

SELECT-OPTIONS s_carrid FOR spfli-carrid.

************************************************************************
CLASS lc_alv_demo DEFINITION.

  PUBLIC SECTION.

    TYPES: BEGIN OF ty_selection,
             r_carrid TYPE RANGE OF spfli-carrid,
           END OF ty_selection.

    METHODS constructor
      IMPORTING is_selection TYPE ty_selection.
    METHODS display.
    METHODS prepare_alv.
    METHODS select.

  PRIVATE SECTION.

    DATA mo_salv TYPE REF TO cl_salv_table.
    DATA ms_selection TYPE ty_selection.
    DATA mt_spfli TYPE STANDARD TABLE OF spfli WITH DEFAULT KEY.

    METHODS filter.
    METHODS modify_columns.
    METHODS sort.

ENDCLASS.
************************************************************************
CLASS lc_alv_demo IMPLEMENTATION.
************************************************************************
  METHOD constructor.

    ms_selection = is_selection.

  ENDMETHOD.
************************************************************************
  METHOD display.

    mo_salv->display( ).

  ENDMETHOD.
************************************************************************
  METHOD filter.

    DATA(lo_filter) = mo_salv->get_filters( ).
    TRY.
        lo_filter->remove_filter( 'FLTIME' ).
        " Flight time up to 8 hours (60 minutes * 8 hours = 480)
        lo_filter->add_filter( columnname = 'FLTIME'
                               sign       = rs_c_range_sign-including
                               option     = rs_c_range_opt-lessequal
                               low        = '480' ).
      CATCH cx_salv_error.
    ENDTRY.

  ENDMETHOD.
************************************************************************
  METHOD modify_columns.

    DATA(lo_columns) = mo_salv->get_columns( ).
    TRY.
        " Client field is set to technical (= no display)
        DATA(lo_column_client) = lo_columns->get_column( 'MANDT' ).
        lo_column_client->set_technical( abap_true ).
      CATCH cx_salv_not_found.
    ENDTRY.

  ENDMETHOD.
************************************************************************
  METHOD prepare_alv.

    TRY.
        CALL METHOD cl_salv_table=>factory
*  EXPORTING
*    list_display   = IF_SALV_C_BOOL_SAP=>FALSE
*    r_container    =
*    container_name =
          IMPORTING
            r_salv_table = mo_salv
          CHANGING
            t_table      = mt_spfli.

        modify_columns( ).
        sort( ).
        filter( ).

      CATCH cx_salv_msg.
    ENDTRY.

  ENDMETHOD.
************************************************************************
  METHOD select.

    SELECT FROM spfli
           FIELDS *
           INTO TABLE @mt_spfli.

  ENDMETHOD.
************************************************************************
  METHOD sort.

    DATA(lo_sorts) = mo_salv->get_sorts( ).
    TRY.
        lo_sorts->add_sort( columnname = 'CARRID'
                            position   = 1
                            sequence   = if_salv_c_sort=>sort_up ).
        lo_sorts->add_sort( columnname = 'CONNID'
                            position   = 2
                            sequence   = if_salv_c_sort=>sort_up ).
      CATCH cx_salv_not_found.
      CATCH cx_salv_existing.
      CATCH cx_salv_data_error.
    ENDTRY.

  ENDMETHOD.
************************************************************************
ENDCLASS.
************************************************************************
START-OF-SELECTION.

  DATA(go_alv_demo) = NEW lc_alv_demo( VALUE #( r_carrid = s_carrid[] ) ).
  go_alv_demo->select( ).
  go_alv_demo->prepare_alv( ).
  go_alv_demo->display( ).
