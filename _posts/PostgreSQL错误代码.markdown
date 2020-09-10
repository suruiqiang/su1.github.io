::: {.preheader}
::: {style="float: left"}
[9.3](/docs/9.3/errcodes-appendix.html)
[9.4](/docs/9.4/errcodes-appendix.html)
[9.5](/docs/9.5/errcodes-appendix.html)
[9.6](/docs/9.6/errcodes-appendix.html)
[10]{style="margin : 0px 5px 0px 5px;"}
[11](/docs/11/errcodes-appendix.html)
[12](/docs/12/errcodes-appendix.html)
:::

::: {style="text-align:right"}
[问题报告](https://github.com/postgres-cn/pgdoc-cn/issues/new "在Github上报告问题（请注明问题内容及所在章节）")
[纠错本页面](https://github.com/postgres-cn/pgdoc-cn/edit/10/postgresql/doc/src/sgml/errcodes.sgml "直接在Github上纠错本页面")
:::

::: {style="position: relative; overflow: hidden; margin: 10px 0;"}
搜索
:::
:::

::: {.navheader xmlns="http://www.w3.org/TR/xhtml1/transitional"}
附录 A. [PostgreSQL]{.productname
xmlns="http://www.w3.org/1999/xhtml"}错误代码
:::

[上一页](appendixes.html "部分 VIII. 附录") 

[上一级](appendixes.html "部分 VIII. 附录")

部分 VIII. 附录

[起始页](index.html "PostgreSQL 10.1 手册")

 [下一页](datetime-appendix.html "附录 B. 日期/时间支持")

------------------------------------------------------------------------

::: {#ERRCODES-APPENDIX .appendix}
::: {.titlepage}
<div>

<div>

附录 A. [PostgreSQL]{.productname}错误代码 {#附录-a.-postgresql错误代码 .title}
------------------------------------------

</div>

</div>
:::

[]{#idm140349368632416 .indexterm}

[PostgreSQL]{.productname}服务器发出的所有消息都被赋予了五个字符错误代码，这遵循
SQL
标准对["[SQLSTATE]{.quote}"]{.quote}代码的习惯。需要知道发生了什么错误条件的应用通常应该测试错误代码，
而不是查看文本形式的错误消息。这些错误代码轻易不会在[PostgreSQL]{.productname}的版本之间改变，并且一般也不会随着错误消息的本地化而改变。请注意有些（但不是全部）[PostgreSQL]{.productname}生成的错误代码是由
SQL
标准定义的；有些标准没有定义的额外错误代码是[PostgreSQL]{.productname}发明的或者是从其它数据库借用的。

根据标准，错误代码的前两个字符表示错误类别，而后三个字符表示在该类别内的一种特定情况。因此，
那些不能识别特定错误代码的应用仍然可以从错误类别中推断要做什么。

[表 A.1](errcodes-appendix.html#ERRCODES-TABLE "表 A.1. PostgreSQL错误代码"){.xref}中列出了[PostgreSQL]{.productname}
10.1定义的所有错误代码（有些实际上目前并没有使用，但是 SQL
标准中有定义）。错误类别也在其中列出。对于每个错误类别都有一个
["[标准]{.quote}"]{.quote}错误代码，它的最后三个字符是`000`{.literal}。这个代码只用于那些属于该类别但是没有被赋予更特定代码的错误情况。

["[情况名称]{.quote}"]{.quote}列中显示的符号是在[PL/pgSQL]{.application}中使用的情况名称。情况名称可以被写成大写或小写形式（注意[PL/pgSQL]{.application}不识别警告（与错误不同）情况名称，它们是类别
00、01 和 02）。

对于某些类型的错误，服务器会报告与错误相关的数据库对象（一个表、表列、数据类型或约束）的名称。例如，导致一个`unique_violation`{.symbol}错误的唯一约束的名称。这些名字被包含在错误报告消息的独立域中，这样应用就不必从可能是本地化的人类可读的消息文本中抽取它们。到[PostgreSQL]{.productname}
9.3 位置，对于这个特性的完全覆盖只在 SQLSTATE 类别 23
（完整性约束未被）的错误中存在，但是很可能会在未来进行扩展。

::: {#ERRCODES-TABLE .table}
**表 A.1. [PostgreSQL]{.productname}错误代码**

::: {.table-contents}
错误代码
:::
:::
:::

情况名称

[**Class 00 --- Successful Completion**]{.bold}

`00000`{.literal}

`successful_completion`{.symbol}

[**Class 01 --- Warning**]{.bold}

`01000`{.literal}

`warning`{.symbol}

`0100C`{.literal}

`dynamic_result_sets_returned`{.symbol}

`01008`{.literal}

`implicit_zero_bit_padding`{.symbol}

`01003`{.literal}

`null_value_eliminated_in_set_function`{.symbol}

`01007`{.literal}

`privilege_not_granted`{.symbol}

`01006`{.literal}

`privilege_not_revoked`{.symbol}

`01004`{.literal}

`string_data_right_truncation`{.symbol}

`01P01`{.literal}

`deprecated_feature`{.symbol}

[**Class 02 --- No Data (this is also a warning class per the SQL
standard)**]{.bold}

`02000`{.literal}

`no_data`{.symbol}

`02001`{.literal}

`no_additional_dynamic_result_sets_returned`{.symbol}

[**Class 03 --- SQL Statement Not Yet Complete**]{.bold}

`03000`{.literal}

`sql_statement_not_yet_complete`{.symbol}

[**Class 08 --- Connection Exception**]{.bold}

`08000`{.literal}

`connection_exception`{.symbol}

`08003`{.literal}

`connection_does_not_exist`{.symbol}

`08006`{.literal}

`connection_failure`{.symbol}

`08001`{.literal}

`sqlclient_unable_to_establish_sqlconnection`{.symbol}

`08004`{.literal}

`sqlserver_rejected_establishment_of_sqlconnection`{.symbol}

`08007`{.literal}

`transaction_resolution_unknown`{.symbol}

`08P01`{.literal}

`protocol_violation`{.symbol}

[**Class 09 --- Triggered Action Exception**]{.bold}

`09000`{.literal}

`triggered_action_exception`{.symbol}

[**Class 0A --- Feature Not Supported**]{.bold}

`0A000`{.literal}

`feature_not_supported`{.symbol}

[**Class 0B --- Invalid Transaction Initiation**]{.bold}

`0B000`{.literal}

`invalid_transaction_initiation`{.symbol}

[**Class 0F --- Locator Exception**]{.bold}

`0F000`{.literal}

`locator_exception`{.symbol}

`0F001`{.literal}

`invalid_locator_specification`{.symbol}

[**Class 0L --- Invalid Grantor**]{.bold}

`0L000`{.literal}

`invalid_grantor`{.symbol}

`0LP01`{.literal}

`invalid_grant_operation`{.symbol}

[**Class 0P --- Invalid Role Specification**]{.bold}

`0P000`{.literal}

`invalid_role_specification`{.symbol}

[**Class 0Z --- Diagnostics Exception**]{.bold}

`0Z000`{.literal}

`diagnostics_exception`{.symbol}

`0Z002`{.literal}

`stacked_diagnostics_accessed_without_active_handler`{.symbol}

[**Class 20 --- Case Not Found**]{.bold}

`20000`{.literal}

`case_not_found`{.symbol}

[**Class 21 --- Cardinality Violation**]{.bold}

`21000`{.literal}

`cardinality_violation`{.symbol}

[**Class 22 --- Data Exception**]{.bold}

`22000`{.literal}

`data_exception`{.symbol}

`2202E`{.literal}

`array_subscript_error`{.symbol}

`22021`{.literal}

`character_not_in_repertoire`{.symbol}

`22008`{.literal}

`datetime_field_overflow`{.symbol}

`22012`{.literal}

`division_by_zero`{.symbol}

`22005`{.literal}

`error_in_assignment`{.symbol}

`2200B`{.literal}

`escape_character_conflict`{.symbol}

`22022`{.literal}

`indicator_overflow`{.symbol}

`22015`{.literal}

`interval_field_overflow`{.symbol}

`2201E`{.literal}

`invalid_argument_for_logarithm`{.symbol}

`22014`{.literal}

`invalid_argument_for_ntile_function`{.symbol}

`22016`{.literal}

`invalid_argument_for_nth_value_function`{.symbol}

`2201F`{.literal}

`invalid_argument_for_power_function`{.symbol}

`2201G`{.literal}

`invalid_argument_for_width_bucket_function`{.symbol}

`22018`{.literal}

`invalid_character_value_for_cast`{.symbol}

`22007`{.literal}

`invalid_datetime_format`{.symbol}

`22019`{.literal}

`invalid_escape_character`{.symbol}

`2200D`{.literal}

`invalid_escape_octet`{.symbol}

`22025`{.literal}

`invalid_escape_sequence`{.symbol}

`22P06`{.literal}

`nonstandard_use_of_escape_character`{.symbol}

`22010`{.literal}

`invalid_indicator_parameter_value`{.symbol}

`22023`{.literal}

`invalid_parameter_value`{.symbol}

`2201B`{.literal}

`invalid_regular_expression`{.symbol}

`2201W`{.literal}

`invalid_row_count_in_limit_clause`{.symbol}

`2201X`{.literal}

`invalid_row_count_in_result_offset_clause`{.symbol}

`2202H`{.literal}

`invalid_tablesample_argument`{.symbol}

`2202G`{.literal}

`invalid_tablesample_repeat`{.symbol}

`22009`{.literal}

`invalid_time_zone_displacement_value`{.symbol}

`2200C`{.literal}

`invalid_use_of_escape_character`{.symbol}

`2200G`{.literal}

`most_specific_type_mismatch`{.symbol}

`22004`{.literal}

`null_value_not_allowed`{.symbol}

`22002`{.literal}

`null_value_no_indicator_parameter`{.symbol}

`22003`{.literal}

`numeric_value_out_of_range`{.symbol}

`2200H`{.literal}

`sequence_generator_limit_exceeded`{.symbol}

`22026`{.literal}

`string_data_length_mismatch`{.symbol}

`22001`{.literal}

`string_data_right_truncation`{.symbol}

`22011`{.literal}

`substring_error`{.symbol}

`22027`{.literal}

`trim_error`{.symbol}

`22024`{.literal}

`unterminated_c_string`{.symbol}

`2200F`{.literal}

`zero_length_character_string`{.symbol}

`22P01`{.literal}

`floating_point_exception`{.symbol}

`22P02`{.literal}

`invalid_text_representation`{.symbol}

`22P03`{.literal}

`invalid_binary_representation`{.symbol}

`22P04`{.literal}

`bad_copy_file_format`{.symbol}

`22P05`{.literal}

`untranslatable_character`{.symbol}

`2200L`{.literal}

`not_an_xml_document`{.symbol}

`2200M`{.literal}

`invalid_xml_document`{.symbol}

`2200N`{.literal}

`invalid_xml_content`{.symbol}

`2200S`{.literal}

`invalid_xml_comment`{.symbol}

`2200T`{.literal}

`invalid_xml_processing_instruction`{.symbol}

[**Class 23 --- Integrity Constraint Violation**]{.bold}

`23000`{.literal}

`integrity_constraint_violation`{.symbol}

`23001`{.literal}

`restrict_violation`{.symbol}

`23502`{.literal}

`not_null_violation`{.symbol}

`23503`{.literal}

`foreign_key_violation`{.symbol}

`23505`{.literal}

`unique_violation`{.symbol}

`23514`{.literal}

`check_violation`{.symbol}

`23P01`{.literal}

`exclusion_violation`{.symbol}

[**Class 24 --- Invalid Cursor State**]{.bold}

`24000`{.literal}

`invalid_cursor_state`{.symbol}

[**Class 25 --- Invalid Transaction State**]{.bold}

`25000`{.literal}

`invalid_transaction_state`{.symbol}

`25001`{.literal}

`active_sql_transaction`{.symbol}

`25002`{.literal}

`branch_transaction_already_active`{.symbol}

`25008`{.literal}

`held_cursor_requires_same_isolation_level`{.symbol}

`25003`{.literal}

`inappropriate_access_mode_for_branch_transaction`{.symbol}

`25004`{.literal}

`inappropriate_isolation_level_for_branch_transaction`{.symbol}

`25005`{.literal}

`no_active_sql_transaction_for_branch_transaction`{.symbol}

`25006`{.literal}

`read_only_sql_transaction`{.symbol}

`25007`{.literal}

`schema_and_data_statement_mixing_not_supported`{.symbol}

`25P01`{.literal}

`no_active_sql_transaction`{.symbol}

`25P02`{.literal}

`in_failed_sql_transaction`{.symbol}

`25P03`{.literal}

`idle_in_transaction_session_timeout`{.symbol}

[**Class 26 --- Invalid SQL Statement Name**]{.bold}

`26000`{.literal}

`invalid_sql_statement_name`{.symbol}

[**Class 27 --- Triggered Data Change Violation**]{.bold}

`27000`{.literal}

`triggered_data_change_violation`{.symbol}

[**Class 28 --- Invalid Authorization Specification**]{.bold}

`28000`{.literal}

`invalid_authorization_specification`{.symbol}

`28P01`{.literal}

`invalid_password`{.symbol}

[**Class 2B --- Dependent Privilege Descriptors Still Exist**]{.bold}

`2B000`{.literal}

`dependent_privilege_descriptors_still_exist`{.symbol}

`2BP01`{.literal}

`dependent_objects_still_exist`{.symbol}

[**Class 2D --- Invalid Transaction Termination**]{.bold}

`2D000`{.literal}

`invalid_transaction_termination`{.symbol}

[**Class 2F --- SQL Routine Exception**]{.bold}

`2F000`{.literal}

`sql_routine_exception`{.symbol}

`2F005`{.literal}

`function_executed_no_return_statement`{.symbol}

`2F002`{.literal}

`modifying_sql_data_not_permitted`{.symbol}

`2F003`{.literal}

`prohibited_sql_statement_attempted`{.symbol}

`2F004`{.literal}

`reading_sql_data_not_permitted`{.symbol}

[**Class 34 --- Invalid Cursor Name**]{.bold}

`34000`{.literal}

`invalid_cursor_name`{.symbol}

[**Class 38 --- External Routine Exception**]{.bold}

`38000`{.literal}

`external_routine_exception`{.symbol}

`38001`{.literal}

`containing_sql_not_permitted`{.symbol}

`38002`{.literal}

`modifying_sql_data_not_permitted`{.symbol}

`38003`{.literal}

`prohibited_sql_statement_attempted`{.symbol}

`38004`{.literal}

`reading_sql_data_not_permitted`{.symbol}

[**Class 39 --- External Routine Invocation Exception**]{.bold}

`39000`{.literal}

`external_routine_invocation_exception`{.symbol}

`39001`{.literal}

`invalid_sqlstate_returned`{.symbol}

`39004`{.literal}

`null_value_not_allowed`{.symbol}

`39P01`{.literal}

`trigger_protocol_violated`{.symbol}

`39P02`{.literal}

`srf_protocol_violated`{.symbol}

`39P03`{.literal}

`event_trigger_protocol_violated`{.symbol}

[**Class 3B --- Savepoint Exception**]{.bold}

`3B000`{.literal}

`savepoint_exception`{.symbol}

`3B001`{.literal}

`invalid_savepoint_specification`{.symbol}

[**Class 3D --- Invalid Catalog Name**]{.bold}

`3D000`{.literal}

`invalid_catalog_name`{.symbol}

[**Class 3F --- Invalid Schema Name**]{.bold}

`3F000`{.literal}

`invalid_schema_name`{.symbol}

[**Class 40 --- Transaction Rollback**]{.bold}

`40000`{.literal}

`transaction_rollback`{.symbol}

`40002`{.literal}

`transaction_integrity_constraint_violation`{.symbol}

`40001`{.literal}

`serialization_failure`{.symbol}

`40003`{.literal}

`statement_completion_unknown`{.symbol}

`40P01`{.literal}

`deadlock_detected`{.symbol}

[**Class 42 --- Syntax Error or Access Rule Violation**]{.bold}

`42000`{.literal}

`syntax_error_or_access_rule_violation`{.symbol}

`42601`{.literal}

`syntax_error`{.symbol}

`42501`{.literal}

`insufficient_privilege`{.symbol}

`42846`{.literal}

`cannot_coerce`{.symbol}

`42803`{.literal}

`grouping_error`{.symbol}

`42P20`{.literal}

`windowing_error`{.symbol}

`42P19`{.literal}

`invalid_recursion`{.symbol}

`42830`{.literal}

`invalid_foreign_key`{.symbol}

`42602`{.literal}

`invalid_name`{.symbol}

`42622`{.literal}

`name_too_long`{.symbol}

`42939`{.literal}

`reserved_name`{.symbol}

`42804`{.literal}

`datatype_mismatch`{.symbol}

`42P18`{.literal}

`indeterminate_datatype`{.symbol}

`42P21`{.literal}

`collation_mismatch`{.symbol}

`42P22`{.literal}

`indeterminate_collation`{.symbol}

`42809`{.literal}

`wrong_object_type`{.symbol}

`428C9`{.literal}

`generated_always`{.symbol}

`42703`{.literal}

`undefined_column`{.symbol}

`42883`{.literal}

`undefined_function`{.symbol}

`42P01`{.literal}

`undefined_table`{.symbol}

`42P02`{.literal}

`undefined_parameter`{.symbol}

`42704`{.literal}

`undefined_object`{.symbol}

`42701`{.literal}

`duplicate_column`{.symbol}

`42P03`{.literal}

`duplicate_cursor`{.symbol}

`42P04`{.literal}

`duplicate_database`{.symbol}

`42723`{.literal}

`duplicate_function`{.symbol}

`42P05`{.literal}

`duplicate_prepared_statement`{.symbol}

`42P06`{.literal}

`duplicate_schema`{.symbol}

`42P07`{.literal}

`duplicate_table`{.symbol}

`42712`{.literal}

`duplicate_alias`{.symbol}

`42710`{.literal}

`duplicate_object`{.symbol}

`42702`{.literal}

`ambiguous_column`{.symbol}

`42725`{.literal}

`ambiguous_function`{.symbol}

`42P08`{.literal}

`ambiguous_parameter`{.symbol}

`42P09`{.literal}

`ambiguous_alias`{.symbol}

`42P10`{.literal}

`invalid_column_reference`{.symbol}

`42611`{.literal}

`invalid_column_definition`{.symbol}

`42P11`{.literal}

`invalid_cursor_definition`{.symbol}

`42P12`{.literal}

`invalid_database_definition`{.symbol}

`42P13`{.literal}

`invalid_function_definition`{.symbol}

`42P14`{.literal}

`invalid_prepared_statement_definition`{.symbol}

`42P15`{.literal}

`invalid_schema_definition`{.symbol}

`42P16`{.literal}

`invalid_table_definition`{.symbol}

`42P17`{.literal}

`invalid_object_definition`{.symbol}

[**Class 44 --- WITH CHECK OPTION Violation**]{.bold}

`44000`{.literal}

`with_check_option_violation`{.symbol}

[**Class 53 --- Insufficient Resources**]{.bold}

`53000`{.literal}

`insufficient_resources`{.symbol}

`53100`{.literal}

`disk_full`{.symbol}

`53200`{.literal}

`out_of_memory`{.symbol}

`53300`{.literal}

`too_many_connections`{.symbol}

`53400`{.literal}

`configuration_limit_exceeded`{.symbol}

[**Class 54 --- Program Limit Exceeded**]{.bold}

`54000`{.literal}

`program_limit_exceeded`{.symbol}

`54001`{.literal}

`statement_too_complex`{.symbol}

`54011`{.literal}

`too_many_columns`{.symbol}

`54023`{.literal}

`too_many_arguments`{.symbol}

[**Class 55 --- Object Not In Prerequisite State**]{.bold}

`55000`{.literal}

`object_not_in_prerequisite_state`{.symbol}

`55006`{.literal}

`object_in_use`{.symbol}

`55P02`{.literal}

`cant_change_runtime_param`{.symbol}

`55P03`{.literal}

`lock_not_available`{.symbol}

[**Class 57 --- Operator Intervention**]{.bold}

`57000`{.literal}

`operator_intervention`{.symbol}

`57014`{.literal}

`query_canceled`{.symbol}

`57P01`{.literal}

`admin_shutdown`{.symbol}

`57P02`{.literal}

`crash_shutdown`{.symbol}

`57P03`{.literal}

`cannot_connect_now`{.symbol}

`57P04`{.literal}

`database_dropped`{.symbol}

[**Class 58 --- System Error (errors external to
[PostgreSQL]{.productname} itself)**]{.bold}

`58000`{.literal}

`system_error`{.symbol}

`58030`{.literal}

`io_error`{.symbol}

`58P01`{.literal}

`undefined_file`{.symbol}

`58P02`{.literal}

`duplicate_file`{.symbol}

[**Class 72 --- Snapshot Failure**]{.bold}

`72000`{.literal}

`snapshot_too_old`{.symbol}

[**Class F0 --- Configuration File Error**]{.bold}

`F0000`{.literal}

`config_file_error`{.symbol}

`F0001`{.literal}

`lock_file_exists`{.symbol}

[**Class HV --- Foreign Data Wrapper Error (SQL/MED)**]{.bold}

`HV000`{.literal}

`fdw_error`{.symbol}

`HV005`{.literal}

`fdw_column_name_not_found`{.symbol}

`HV002`{.literal}

`fdw_dynamic_parameter_value_needed`{.symbol}

`HV010`{.literal}

`fdw_function_sequence_error`{.symbol}

`HV021`{.literal}

`fdw_inconsistent_descriptor_information`{.symbol}

`HV024`{.literal}

`fdw_invalid_attribute_value`{.symbol}

`HV007`{.literal}

`fdw_invalid_column_name`{.symbol}

`HV008`{.literal}

`fdw_invalid_column_number`{.symbol}

`HV004`{.literal}

`fdw_invalid_data_type`{.symbol}

`HV006`{.literal}

`fdw_invalid_data_type_descriptors`{.symbol}

`HV091`{.literal}

`fdw_invalid_descriptor_field_identifier`{.symbol}

`HV00B`{.literal}

`fdw_invalid_handle`{.symbol}

`HV00C`{.literal}

`fdw_invalid_option_index`{.symbol}

`HV00D`{.literal}

`fdw_invalid_option_name`{.symbol}

`HV090`{.literal}

`fdw_invalid_string_length_or_buffer_length`{.symbol}

`HV00A`{.literal}

`fdw_invalid_string_format`{.symbol}

`HV009`{.literal}

`fdw_invalid_use_of_null_pointer`{.symbol}

`HV014`{.literal}

`fdw_too_many_handles`{.symbol}

`HV001`{.literal}

`fdw_out_of_memory`{.symbol}

`HV00P`{.literal}

`fdw_no_schemas`{.symbol}

`HV00J`{.literal}

`fdw_option_name_not_found`{.symbol}

`HV00K`{.literal}

`fdw_reply_handle`{.symbol}

`HV00Q`{.literal}

`fdw_schema_not_found`{.symbol}

`HV00R`{.literal}

`fdw_table_not_found`{.symbol}

`HV00L`{.literal}

`fdw_unable_to_create_execution`{.symbol}

`HV00M`{.literal}

`fdw_unable_to_create_reply`{.symbol}

`HV00N`{.literal}

`fdw_unable_to_establish_connection`{.symbol}

[**Class P0 --- PL/pgSQL Error**]{.bold}

`P0000`{.literal}

`plpgsql_error`{.symbol}

`P0001`{.literal}

`raise_exception`{.symbol}

`P0002`{.literal}

`no_data_found`{.symbol}

`P0003`{.literal}

`too_many_rows`{.symbol}

`P0004`{.literal}

`assert_failure`{.symbol}

[**Class XX --- Internal Error**]{.bold}

`XX000`{.literal}

`internal_error`{.symbol}

`XX001`{.literal}

`data_corrupted`{.symbol}

`XX002`{.literal}

`index_corrupted`{.symbol}

\

::: {.navfooter}

------------------------------------------------------------------------

  ---------------------------- --------------------------- -----------------------------------
  [上一页](appendixes.html)     [上一级](appendixes.html)     [下一页](datetime-appendix.html)
  部分 VIII. 附录                 [起始页](index.html)                   附录 B. 日期/时间支持
  ---------------------------- --------------------------- -----------------------------------
:::
