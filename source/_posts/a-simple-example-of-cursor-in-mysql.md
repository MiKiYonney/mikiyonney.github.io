title: A simple example of cursor in mysql
date: 2014-04-15 14:19:05
tags: [mysql,db]
categories: Db
---

##mysql中游标的使用

因为项目需要，复习下游标的使用方法！记录如下：

<!-- more -->	
```
CREATE PROCEDURE `cursor_updateAreaAndTask`()
BEGIN
  declare projectId int(10);
  declare hotelcode varchar(7);
  declare areacode varchar(255);
  declare doneArea int;  
  declare privilege varchar(255);  
  declare asktoRepair varchar(255);  
  -- 更新区域的游标(所有记录都查出来)
	DECLARE cur_updArea CURSOR FOR
	  (SELECT r.ProjectRepairReqID, r.Hotel,h.AreaCode,r.Privilege,r.IsValidateByUser from quickas_projectrepairreq r, quickas_hotel h WHERE r.Hotel  =  h.HotelCode );
  
  DECLARE CONTINUE HANDLER FOR NOT FOUND SET doneArea = 1;
 
  open cur_updArea;  
  cur_updArea:loop
    fetch cur_updArea into projectId, hotelcode,areacode,privilege,asktoRepair;  
    if doneArea=1 then
			leave cur_updArea;
    end if;
    UPDATE quickas_projectrepairreq SET Area = areacode where ProjectRepairReqID = projectId;
     -- 插入task表
    if privilege = '1' THEN  -- 硬件问题
       insert INTO quickas_workflow_tasks(instanceId,nodeId,roleid,`status`,operationId,receiveTime,creatTime,handleTime,`logs`) VALUES (projectId,1,asktoRepair,0,0,addDate(CURRENT_TIMESTAMP, 2),CURRENT_TIMESTAMP,addDate(CURRENT_TIMESTAMP, 2),'旧工单迁移');
    ELSEIF privilege = '2' THEN  -- 软件问题
      insert INTO quickas_workflow_tasks(instanceId,nodeId,roleid,`status`,operationId,receiveTime,creatTime,handleTime,`logs`) VALUES (projectId,7,asktoRepair,0,0,addDate(CURRENT_TIMESTAMP, 2),CURRENT_TIMESTAMP,addDate(CURRENT_TIMESTAMP, 2),'旧工单迁移');
    end if;
  end LOOP cur_updArea;
  close cur_updArea;  
	END;
```
