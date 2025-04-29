@Path("/kycAdd")
@POST
@AuthPermission(value = BaseService.MANAGE_PERMISSION)
public Response kycAdd(List<KycLimit> kycLimitList) {
    Map<String, Object> resp = new LinkedHashMap<>();
    KycLimitManager kycLimitManager = getManager();

    for (KycLimit dtoLimit : kycLimitList) {

        KycLimit existingLimit = kycLimitManager.getById(dtoLimit.getId());

        if (existingLimit == null) {
            resp.put(JSON_KEY_MSG, "KYC Limit not found for ID: " + dtoLimit.getId());
            resp.put(JSON_KEY_SUCCESS, Boolean.FALSE);
            return Response.ok(resp, MediaType.APPLICATION_JSON).build();
        }

        if (!isChanged(existingLimit, dtoLimit)) {
            return error(Response.Status.NOT_MODIFIED, "No changes detected for ID: " + dtoLimit.getId());
        }

        String kycConfig;
        if (StringUtils.isNotBlank(existingLimit.getType())) {
            kycConfig = existingLimit.getType().toLowerCase() + existingLimit.getInitType().toLowerCase();
        } else {
            resp.put(JSON_KEY_MSG, "KYC Format is Invalid for ID: " + dtoLimit.getId());
            resp.put(JSON_KEY_SUCCESS, Boolean.FALSE);
            return Response.ok(resp, MediaType.APPLICATION_JSON).build();
        }

        if (!validateRequest(dtoLimit, kycConfig, resp)) {
            return Response.ok(resp, MediaType.APPLICATION_JSON).build();
        }

        // Step 1: Mark Old Record INACTIVE
        existingLimit.setStatus("INACTIVE");
        existingLimit.setUpdatedAt(new Date());
        existingLimit.setUpdatedBy("user"); // replace "user" with logged-in user if available
        kycLimitManager.saveOrUpdate(existingLimit);

        // Step 2: Create New Record ACTIVE
        KycLimit newLimit = new KycLimit();
        BeanUtils.copyProperties(dtoLimit, newLimit);

        newLimit.setId(null); // Generate new ID
        newLimit.setStatus("ACTIVE");
        newLimit.setCreatedAt(new Date());
        newLimit.setCreatedBy("user"); // replace "user" with logged-in user if available

        // Important: Update Limit Fields from dto
        newLimit.setCapacityLimit(dtoLimit.getCapacityLimit());
        newLimit.setPerDayLoadLimit(dtoLimit.getPerDayLoadLimit());
        newLimit.setPerDayUnLoadLimit(dtoLimit.getPerDayUnLoadLimit());
        newLimit.setPerDayTrfInwardLimit(dtoLimit.getPerDayTrfInwardLimit());
        newLimit.setPerDayTfrOutwardLimit(dtoLimit.getPerDayTfrOutwardLimit());
        newLimit.setTxnLoadCount(dtoLimit.getTxnLoadCount());
        newLimit.setTxnTfrOutwardCount(dtoLimit.getTxnTfrOutwardCount());
        newLimit.setPerTransaction(dtoLimit.getPerTransaction());
        newLimit.setMonthlyTfrOutwardCount(dtoLimit.getMonthlyTfrOutwardCount());

        kycLimitManager.saveOrUpdate(newLimit);

        // Step 3: Send to RTSP Switch if Type Normal
        if (StringUtils.equalsIgnoreCase(newLimit.getType(), KYC_LIMIT_TYPE_NORMAL)) {
            sendToRTSPSwitch(newLimit);
        }
    }

    resp.put(JSON_KEY_MSG, "KYC Limit Updated Successfully.");
    resp.put(JSON_KEY_SUCCESS, Boolean.TRUE);
    return Response.ok(resp, MediaType.APPLICATION_JSON).build();
}







@Path("/kycAdd")
@POST
@AuthPermission(value = BaseService.MANAGE_PERMISSION)
public Response kycAdd(List<KycLimit> kycLimitList) {
    Map<String, Object> resp = new LinkedHashMap<>();
    KycLimitManager kycLimitManager = getManager();

    for (KycLimit dtoLimit : kycLimitList) {

        KycLimit existingLimit = kycLimitManager.getById(dtoLimit.getId());

        if (existingLimit == null) {
            resp.put(JSON_KEY_MSG, "KYC Limit not found for ID: " + dtoLimit.getId());
            resp.put(JSON_KEY_SUCCESS, Boolean.FALSE);
            return Response.ok(resp, MediaType.APPLICATION_JSON).build();
        }

        if (!isChanged(existingLimit, dtoLimit)) {
            return error(Response.Status.NOT_MODIFIED, "No changes detected for ID: " + dtoLimit.getId());
        }

        String kycConfig;
        if (StringUtils.isNotBlank(existingLimit.getType())) {
            kycConfig = existingLimit.getType().toLowerCase() + existingLimit.getInitType().toLowerCase();
        } else {
            resp.put(JSON_KEY_MSG, "KYC Format is Invalid for ID: " + dtoLimit.getId());
            resp.put(JSON_KEY_SUCCESS, Boolean.FALSE);
            return Response.ok(resp, MediaType.APPLICATION_JSON).build();
        }

        if (!validateRequest(dtoLimit, kycConfig, resp)) {
            return Response.ok(resp, MediaType.APPLICATION_JSON).build();
        }

        // Step 1: Mark Old Record INACTIVE
        existingLimit.setStatus("INACTIVE");
        existingLimit.setUpdatedAt(new Date());
        existingLimit.setUpdatedBy("user"); // replace "user" with logged-in user if available
        kycLimitManager.saveOrUpdate(existingLimit);

        // Step 2: Create New Record ACTIVE
        KycLimit newLimit = new KycLimit();
        BeanUtils.copyProperties(dtoLimit, newLimit);

        newLimit.setId(null); // Generate new ID
        newLimit.setStatus("ACTIVE");
        newLimit.setCreatedAt(new Date());
        newLimit.setCreatedBy("user"); // replace "user" with logged-in user if available

        // Important: Update Limit Fields from dto
        newLimit.setCapacityLimit(dtoLimit.getCapacityLimit());
        newLimit.setPerDayLoadLimit(dtoLimit.getPerDayLoadLimit());
        newLimit.setPerDayUnLoadLimit(dtoLimit.getPerDayUnLoadLimit());
        newLimit.setPerDayTrfInwardLimit(dtoLimit.getPerDayTrfInwardLimit());
        newLimit.setPerDayTfrOutwardLimit(dtoLimit.getPerDayTfrOutwardLimit());
        newLimit.setTxnLoadCount(dtoLimit.getTxnLoadCount());
        newLimit.setTxnTfrOutwardCount(dtoLimit.getTxnTfrOutwardCount());
        newLimit.setPerTransaction(dtoLimit.getPerTransaction());
        newLimit.setMonthlyTfrOutwardCount(dtoLimit.getMonthlyTfrOutwardCount());

        kycLimitManager.saveOrUpdate(newLimit);

        // Step 3: Send to RTSP Switch if Type Normal
        if (StringUtils.equalsIgnoreCase(newLimit.getType(), KYC_LIMIT_TYPE_NORMAL)) {
            sendToRTSPSwitch(newLimit);
        }
    }

    resp.put(JSON_KEY_MSG, "KYC Limit Updated Successfully.");
    resp.put(JSON_KEY_SUCCESS, Boolean.TRUE);
    return Response.ok(resp, MediaType.APPLICATION_JSON).build();
}

