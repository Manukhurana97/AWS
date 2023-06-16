# AWS
AWS Course Material

1. Certified Developer: Associate :- ( https://courses.datacumulus.com/downloads/certified-developer-k92/ )
2. Certified Cloud Solution Architect: Associate: ( https://courses.datacumulus.com/downloads/certified-solutions-architect-pn9/ )
SELECT
    requestId,
    requestStatus,
    requesterId,
    requesterName,
    delegateId,
    delegateName,
    mnemonic,
    appName,
    createDate,
    reviewerId,
    reviewerName,
    updateDate,
    version,
    remarks,
    isRollback,
    rfc_number,
    next_regionId
FROM (
    SELECT
        aso.request_id AS requestId,
        aow.request_status AS requestStatus,
        aso.app_mnemonic AS mnemonic,
        aso.app_name AS appName,
        aso.create_date AS createDate,
        aow.requester_id AS requesterId,
        aow.REQUESTER_FULL_NAME AS requesterName,
        aow.DELEGATE_ID AS delegateId,
        aow.DELEGATE_FULL_NAME AS delegateName,
        aow.reviewer_id AS reviewerId,
        aow.REVIEWER_FULL_NAME AS reviewerName,
        aow.update_date AS updateDate,
        aow.VERSION AS version,
        aow.REMARKS AS remarks,
        aow.ISROLLBACK AS isRollback,
        aow.RFC_NUMBER AS rfc_number,
        aow.NEXT_REGION_ID AS next_regionId,
        ROW_NUMBER() OVER (PARTITION BY aow.request_id ORDER BY aow.update_date DESC) AS ROW_NUMBER
    FROM
        alerts_onboarding_workflow aow
    INNER JOIN
        alerts_ss_onboarding aso ON aow.request_id = aso.request_id
) AS subquery1
WHERE ROW_NUMBER = 1

UNION

SELECT
    NULL AS requestId,
    NULL AS requestStatus,
    NULL AS requesterId,
    NULL AS requesterName,
    NULL AS delegateId,
    NULL AS delegateName,
    NULL AS mnemonic,
    NULL AS appName,
    NULL AS createDate,
    NULL AS reviewerId,
    NULL AS reviewerName,
    NULL AS updateDate,
    VERSION AS version,
    NULL AS remarks,
    NULL AS isRollback,
    NULL AS rfc_number,
    NULL AS next_regionId
FROM (
    SELECT
        WORKFLOW_ID,
        VERSION,
        ROW_NUMBER() OVER (PARTITION BY REQUEST_ID ORDER BY WORKFLOW_ID DESC) AS ROWNUMBER
    FROM
        ALERTS_ONBOARDING_WORKFLOW
    WHERE
        request_status = 'DEPLOYED'
) AS subquery2
WHERE ROWNUMBER = 1;
