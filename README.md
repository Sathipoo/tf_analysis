# tf_analysis


SELECT CAST(
    'SELECT A.* FROM (' + 
        LEFT(A.test, LEN(A.test) - 9) + 
    ') A WHERE A.time_stamp >= DATEADD(mi, DATEDIFF(mi, GETDATE(), GETUTCDATE()), ''' + '$LastRunTime' + ''' )'
    AS VARCHAR(8000)
) AS Param
FROM (
    SELECT STUFF((
        SELECT 
            'SELECT time_stamp, CS_' + SUBSTRING(table_name, 4, 4) + '_CS_BGT_AWPD_COMP_ID_ID AS CS_YYYY_CS_BGT_AWPD_COMP_ID_ID, u2_id AS STUDENT_ID, ' + 
            SUBSTRING(table_name, 4, 4) + ' AS ACADEMIC_YEAR ' +
            'FROM ' + TABLE_SCHEMA + '.' + TABLE_NAME + ' UNION ALL '
        FROM INFORMATION_SCHEMA.TABLES
        WHERE TABLE_NAME LIKE 'CS\_\_%\_CS\_BGT\_AWPD\_COMP\_ID' ESCAPE '\'
        FOR XML PATH(''), TYPE
    ).value('.', 'NVARCHAR(MAX)'), 1, 0, '') AS test
) A;


'SELECT A.* FROM (' || LEFT(dynamic_sql, LENGTH(dynamic_sql) - 9) || ') A ' ||
'WHERE A.time_stamp >= DATEADD(mi, DATEDIFF(mi, GETDATE(), GETUTCDATE()), ''' || TO_CHAR($LastRunTime, 'YYYY-MM-DD HH24:MI:SS') || ''')'
