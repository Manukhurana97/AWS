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
    next_regionId,
    deployedVersion
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
LEFT JOIN (
    SELECT
        REQUEST_ID,
        VERSION AS deployedVersion,
        ROW_NUMBER() OVER (PARTITION BY REQUEST_ID ORDER BY WORKFLOW_ID DESC) AS ROWNUMBER
    FROM
        ALERTS_ONBOARDING_WORKFLOW
    WHERE
        request_status = 'DEPLOYED'
) AS subquery2 ON subquery1.requestId = subquery2.REQUEST_ID AND subquery2.ROWNUMBER = 1;

