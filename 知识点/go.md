```java

  public List<Long> findOrderIdByFundingProviderWithoutData(Collection<CashLoanFundingProviderType> fundingProviderTypes) {
    List<String> fundingProviderCodes = fundingProviderTypes.stream().map(f -> f.code).collect(Collectors.toList());
    return create()
        .select()
        .from(Tables.CASH_LOAN_ORDER)
        .join(table)
        .on(table.ORDER_ID.eq(Tables.CASH_LOAN_ORDER.ID))
        .where(Tables.CASH_LOAN_ORDER.FUNDING_PROVIDER.in(fundingProviderCodes))
        .orderBy(Tables.CASH_LOAN_ORDER.ID)
        .fetch(Tables.CASH_LOAN_ORDER.ID);
  }
```

```java
package com.yqg.scheduler.jobs.backfill;

import com.google.common.collect.Lists;
import com.yqg.contract.common.mvc.response.contract.ContractResponse;
import com.yqg.contract.common.mvc.vo.TypeVersion;
import com.yqg.core.model.sql.cashloan.CashLoanOrderBadLoanCompensateModel;
import com.yqg.core.model.sql.contract.enums.ContractType;
import com.yqg.core.service.cashloan.ExecutorFactory;
import com.yqg.core.service.cashloan.funding.CashLoanFundingProviderType;
import com.yqg.core.service.contract.ContractServiceV2;
import com.yqg.scheduler.base.CashLoanBaseJob;
import com.yqg.scheduler.vo.JobExecutionContext;
import org.springframework.beans.factory.annotation.Autowired;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Future;

public class BackFillSignContractToNATION_TRUSTJob extends CashLoanBaseJob {

  @Autowired
  private CashLoanOrderBadLoanCompensateModel cashLoanOrderBadLoanCompensateModel;

  @Autowired
  private ContractServiceV2 contractServiceV2;

  @Override
  protected void doExecute(JobExecutionContext jobExecutionContext) throws Exception {
    ExecutorService executorService = ExecutorFactory.getDefault(10);
    while (!isInterrupted()) {
      List<CashLoanFundingProviderType> list = Collections.singletonList(CashLoanFundingProviderType.NATIONAL_TRUST);
      List<Long> orderIds = cashLoanOrderBadLoanCompensateModel.findOrderIdByFundingProviderWithoutData(list);
      if (orderIds == null || orderIds.isEmpty()) {
        log.info("BackFillSignContractToNATION_TRUSTJob finished...");
        return;
      }
      List<Future> futures = new ArrayList<>();
      List<List<Long>> tasks = Lists.partition(orderIds, 10);
      tasks.forEach(task -> {
        futures.add(executorService.submit(() -> {
          task.forEach(id -> {
            ContractResponse contractResponse = contractServiceV2.getContract(String.valueOf(id),
                new TypeVersion(ContractType.P2P_DEBT_CLAIM_ASSIGNMENT_AGREEMENT.name(), "1"));
          });
        }));
      });
    }
  }
}

```

