# test-new-repo

SELECT NVL(SUM(ter.reslu_carton_qty), 0)
FROM tips_exception te
INNER JOIN tips_ex_reslu ter ON ter.payment_office_id = te.payment_office_id
                             AND ter.exception_id = te.exception_id
                             AND ter.cancel_ind = 'N'
INNER JOIN tips_ex_type tet ON tet.ex_type_code = te.ex_type_code
WHERE te.quote_id = 13099949
  AND te.quote_ship_nbr = 1
  AND (
    tet.pri_expense_type IN (
      SELECT NVL(carton_exp_type_cd, 0)
      FROM lsr_exp_carton
      WHERE sub_exp_type_cd IN (
        SELECT expense_type
        FROM exp_temp
      )
      AND purchase_cmpny_id = 1002
    )
    OR tet.pri_expense_type IN (
      SELECT expense_type
      FROM exp_temp
    )
  )
  AND NOT EXISTS (
    SELECT 1
    FROM tips_reslu_ex_type tret
    WHERE tret.payment_office_id = ter.payment_office_id
      AND tret.ex_type_code = te.ex_type_code
      AND tret.reslu_type_code = ter.reslu_type_code
      AND tret.apply_to_exp_ind = 'N'
  );
