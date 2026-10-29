<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import { groupByCause, uniqueById, type Cause, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  const ticket = 4471;
  const repo = 'https://github.com/krzysiekkuligowski/mega-s01e03-what-it-shows';
  const issues: Record<Cause, number> = { parser: 1, hint: 2 };
  const names: Record<Cause, string> = {
    parser: 'Day-first dates are parsed month-first',
    hint: 'booking-v3 suggests MM/DD/YYYY for day-first tenants',
  };
  const explanations: Record<Cause, string> = {
    parser: 'The shared parser treats a day-first tenant date as month-first, so the month and day flip.',
    hint: 'The booking-v3 form tells people to enter MM/DD/YYYY, then reads that value in the tenant locale.',
  };

  const merged = uniqueById(reports as Report[]).filter((report) => report.mergedInto === ticket);
  const groups = groupByCause(merged, tenantOf).filter((group): group is typeof group & { cause: Cause } => group.cause !== 'unplaced');
</script>

<p><strong>{merged.length}</strong> unique reports were merged into #{ticket}. They describe two causes, not one.</p>

<table>
  <thead>
    <tr><th>ticket</th><th>cause</th><th>reports</th><th>tenants</th><th>seen</th></tr>
  </thead>
  <tbody>
    {#each groups as group (group.cause)}
      <tr>
        <td><a href={`${repo}/issues/${issues[group.cause]}`}>#{issues[group.cause]}</a></td>
        <td><strong>{names[group.cause]}</strong><br /><span>{explanations[group.cause]}</span></td>
        <td>{group.reports.length}</td>
        <td>{group.tenants}</td>
        <td>{group.firstSeen} → {group.lastSeen}</td>
      </tr>
    {/each}
  </tbody>
</table>

<style>
  table { width: 100%; border-collapse: collapse; }
  th, td { padding: 0.6rem; border-bottom: 1px solid var(--line); text-align: left; vertical-align: top; }
  span { color: var(--muted); font-size: 0.9em; }
</style>
