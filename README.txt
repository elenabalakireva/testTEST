@Repository
public class CompanyDocVersionCustomRepository {
    
    @PersistenceContext
    private EntityManager entityManager;
    
    @Transactional
    public void updateFiles(File file) {
        String sql = String.format("""
            UPDATE company_doc_version 
            SET files = COALESCE(
                (
                    SELECT jsonb_agg(
                        jsonb_set(
                            jsonb_set(
                                jsonb_set(
                                    jsonb_set(
                                        jsonb_set(
                                            jsonb_set(
                                                jsonb_set(
                                                    elem,
                                                    '{id}',
                                                    '"%s"'
                                                ),
                                                '{md5}',
                                                '"%s"'
                                            ),
                                            '{name}',
                                                '"%s"'
                                        ),
                                        '{filesize}',
                                        '%d'
                                    ),
                                    '{mimeType}',
                                    '"%s"'
                                ),
                                '{extension}',
                                '"%s"'
                            ),
                            '{displayName}',
                            '"%s"'
                        )
                    )
                    FROM jsonb_array_elements(files) AS elem
                ),
                '[]'::jsonb
            )
            WHERE id BETWEEN 760 AND 761 
            AND files IS NOT NULL
            """, 
            file.getId(), 
            file.getMd5(), 
            file.getName(), 
            file.getFileSize(),
            file.getMimeType(), 
            file.getExtension(), 
            file.getDisplayName());
            
        Query query = entityManager.createNativeQuery(sql);
